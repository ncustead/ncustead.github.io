# Privileged Access

There are several ways to move around a Domain: Pass-the-Hash, RDP, PowerShell Remoting, MSSQL. These can be enumerated by running BloodHound.

* RDP Enumeration Command will be: Get-NetLocalGroupMember -ComputerName -GroupName "Remote Desktop Users"
* Remote Management Enumeration
  * Command will be: Get-NetLocalGroupMember -ComputerName -GroupName "Remote Management Users"
* Bloodhound Query for the same process
  * In Raw Query box at bottom of screen, paste:
  * MATCH p1=shortestPath((u1:User)-\[r1:MemberOf_1..]->(g1:Group)) MATCH p2=(u1)-\[:CanPSRemote_1..]->(c:Computer) RETURN p2
* Establishing WinRM Session from Windows
  * $password = ConvertTo-SecureString "" -AsPlainText -Force
  * $cred = new-object System.Management.Automation.PSCredential ("INLANEFREIGHT\<user\_with\_RemoteManagementPrivs>", $password)
  * Enter-PSSession -ComputerName -Credential $cred

\-From Linux/Kali AP - evil-winrm -i -u \<user\_with\_knownpassword>
