![[Pasted image 20230727212856.png]]
Let's say we have three hosts: `Attack host` --> `DEV01` --> `DC01`. Our Attack Host is a Parrot box within the corporate network but not joined to the domain. We obtain a set of credentials for a domain user and find that they are part of the `Remote Management Users` group on DEV01. We want to use `PowerView` to enumerate the domain, which requires communication with the Domain Controller, DC01.
wsmprovhost.exe - Windows Remote PowerShell session

Evil-WinRM is a Kali AP tool that will let us connect to a Windows machine. 
	evil-winrm -i <IPaddr> -u <user/SPN>
Scenario #1 from evil-winrm
	- import-module .\PowerView.ps1
	- klist will show cached kerberos tickets for current server (dev01 in image)
	- $newpassword = ConvertTo-SecureString '!qazXSW@' -AsPlainText -Force
	- $cred = New-Object System.Management.Automation.PSCredential('INLANE\backupadm', $newpass)

Scenario #2 from PSSession-Configuration Registration
Domain joined host that can connect to another domain host using WinRm
Scenario uses backupadm again
From PowerShell session on victim host you can run:
- Register-PSSessionConfiguration -Name backupadmsess -RunAsCredential inlanefreight\backupadm 
- Enter-PSSession -ComputerName DEV01 -Credential INLANEFREIGHT\backupadm -ConfigurationName  backupadmsess

After running these two, you should be able to query SPNs and do cross domain enumeration


