
- [ ]  Create Exam Tabs for each Target

- [x] Start Auto Enumeration 
```c
autorecon 192.168.198.199 --dirbuster.ext txt,pdf,php,html

autorecon --dirbuster.ext php,html,txt,pdf,aspx -t targets
```


- [x] Start Manual Enumeration for ports that pop up
	Start off with:
```c
nmap -p- --min-rate 5000 -T4 <ip address>
``` 

	Followed by:
```c
nmap -sU for udp ports
```

	Then run:
```c
nmap -sV -sC on the ports I found.
```

- [ ] Then nc them:
```c
nc -nv 192.168.214.145 3003
```

- [x] Also google any ports that dont return a lot of info

```c
FUZZ DIRECTORIES:
export URL="https://example.com/FUZZ/"
wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/raft-medium-directories.txt --hc 404 "$URL"

FUZZ FILES:
wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/raft-medium-files.txt --hc 404 "$URL"
```

If websites, try to find vhost name and add to /etc/hosts

Check UDP as well:
```c
nmap -sC -sV -O -sU 192.168.204.153
```

- [ ] [HTTPS](obsidian://open?vault=Nicks%20OSCP%20Notes&file=Cheatsheets%2FProtocols%2FHTTP(S)%2F01.%20Enumeration%20Checklist)
	- [ ] Checkout what the webpage displays
	- [ ] Directory search recursively with dirsearch and -r
	- [ ] sqli injection in fields, try in burp
	- [ ] path traversal, is there a php?= or page?= or some other php verbage?
	- [ ] search all names and versions on google for POC
	- [ ] check all end points, if api curl -i
	- [ ] if wordpress, use wpscan, get a token from website
	- [ ] Alt or internal hosting? Update /etc/hosts
	- [ ] Read entire pages. Enumerate for emails, names, user info etc.
	- [ ] Enum the interface, version of CMS? Server installation page?
	- [ ] Potential vulnerability? 
		- [ ] [LFI](obsidian://open?vault=Nicks%20OSCP%20Notes&file=Cheatsheets%2FCommon%20Exploits%2FLFI), RFI
		- [ ] [XSS](obsidian://open?vault=Nicks%20OSC&file=Cheatsheets%2FTools%2Fxss)
		- [ ] [Upload](obsidian://open?vault=Nicks%20OSC&file=Cheatsheets%2FCommon%20Exploits%2FUpload)
	- [ ] Default web server page, version information
	- [ ] View source code
		- [ ]  Hidden Values
		- [ ] Developer Remarks
		- [ ] Extraneous Code
		- [ ]  Passwords
	- [ ] Robots.txt
	- [ ] Web scanning
	- [ ] If theres an upload, set up responder to listen and direct the upload to an impacket-smb share
- [ ] [SSH](obsidian://open?vault=Nicks%20OSC&file=Cheatsheets%2FProtocols%2F21%20FTP)
	- [ ] Do you have any creds, from any where
	- [ ] hydra
	- [ ] always check for keys if ssh is open
- [ ] [FTP](obsidian://open?vault=Nicks%20OSC&file=Cheatsheets%2FProtocols%2F22%20SSH)
	- [ ] Do you have any creds, from any where
	- [ ] Anonymous login?
- [ ] [SNMP](obsidian://open?vault=Nicks%20OSCP%20Notes&file=Labs%2FOSCP-B%2FMS02%20-%20DC01)
	- [ ] snmpwalk -v2c -c public 192.168.204.149 NET-SNMP-EXTEND-MIB::nsExtendObjects
	- [ ] Search in the process list for mouse and browse processes
- [ ] SMB
	- [ ] nmap --script smb-vuln* -p 139,445 -oN smb-vuln-scan 192.168.227.40
- [ ] LDAP
	- [ ] nmap -sV --script "ldap* and not brute" $IP
	- [ ] Is AD also reachable? LDAP query:
		- [ ] ldapsearch -x -v -b "DC=oscp, DC=exam" -H "ldap://192.168.1.1" -p 389
- [ ] RPC
	- [ ] rpcinfo -p $ip
- [ ] Windows Services
	- [ ] If you have creds or hashes try crackmapexec for all types
	- [ ] Try hashes, try psexec
	- [ ] smbclient -L
- [ ] LFI
	- [ ] Check for /etc/passwd and add to users text file
	- [ ] 


## Enumeration as low
- [ ] whoami /priv
	- [ ] SEImpersonate -- potato or printspoofer
	- [ ] if everything is enabled -- execute power view then, Get-GPO -Name "Default Domain Policy", then
		- [ ] Get-GPPermission -Guid (long number from the previous command) -TargetType user -TargetName (current_user)
			- [ ]  SharpGPOAbuse.exe --AddLocalAdmin --UserAccount (user) --GPOName "Default Domain Policy"
			- [ ] gpupdate /force
- [ ] run linpeas
- [ ] run winpeas AND powerup/powerview/mimikatz
- [ ] Check for UAC if an adminsitrator(see UAC bypass page)
- [ ] check user dirs
- [ ] check /opt, /etc
- [ ] check netstat to see if internal services running
- [ ] check .bash_history or Get-History 
- [ ] check for keepass
- [ ] check for windows.old (use mimikatz to crack, refer to [here](obsidian://open?vault=Nicks%20OSCP%20Notes&file=Labs%2FOSCP-B%2FMS02%20-%20DC01))
- [ ] 

use powershell to search to .kdbx databases of saved passwords
```
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```


## Active Directory Pivot/PrivEsc
- [ ] Track all user names, passwords and hashes
	- [ ] Spray them using crackmap
- [ ] mimikatz everything
- [ ] Rubeus asp-roast and kerberoast
- [ ] https://wadcoms.github.io/

##### Additional Resources:
https://github.com/0xsyr0/OSCP