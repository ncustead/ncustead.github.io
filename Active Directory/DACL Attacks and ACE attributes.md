![[DACL.png]]

-  GUID value 00299570-246d-11d0-a768-00aa006e0529. This is important for Windows because this represents the User Force Change Password

- From PowerShell, you can run a few lines to enumerate a Domain:
	- Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt

- Next you can run a foreach loop to identify the following: ACL information for each domain user, Access Rights for each user, and then identify properties of each users we are in control for.
	- Example loop used:
		- ```powershell-session foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}

- In the above example, we had the password for user wley. We used the domain enumeration script, then found what rights wley had in the domain. The result of this command showed us we have control of another user (damundsen) via the User Force Change Password right. You can now take this and see where it takes you. For further reference, I will link the page![[AD tactics.pdf]]