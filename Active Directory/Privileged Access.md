There are several ways to move around a Domain:
	Pass-the-Hash, RDP, PowerShell Remoting, MSSQL. These can be enumerated by running BloodHound.

- RDP Enumeration
	Command will be: Get-NetLocalGroupMember -ComputerName <targetHost> -GroupName "Remote Desktop Users"

- Remote Management Enumeration
	- Command will be: Get-NetLocalGroupMember -ComputerName <targetHost> -GroupName "Remote Management Users"

- Bloodhound Query for the same process
	- In Raw Query box at bottom of screen, paste:
	- MATCH p1=shortestPath((u1:User)-[r1:MemberOf*1..]->(g1:Group)) MATCH p2=(u1)-[:CanPSRemote*1..]->(c:Computer) RETURN p2

- Establishing WinRM Session from Windows
	- $password = ConvertTo-SecureString "<password>" -AsPlainText -Force
	- $cred = new-object System.Management.Automation.PSCredential ("INLANEFREIGHT\<user_with_RemoteManagementPrivs>", $password)
	- Enter-PSSession -ComputerName <targetHost> -Credential $cred

-From Linux/Kali AP
	- evil-winrm -i <IPaddr> -u <user_with_knownpassword>