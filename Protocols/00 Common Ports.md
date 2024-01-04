| Port | Thoughts |
|------|----------|
|21|FTP server, unencrypted.|
|22|SSH server, can be connected to via SSH|
|23|Telnet. Basically an unencrypted SSH|
|25|SMTP - Email sending service. Query it to enum email addresses?|
| 69 | TFTP Server. Very uncommon and old. Uses UDP.|
| 80 | HTTP Server, hosting website? Try visiting IP with web browser |
| 88 | Kerboros Service. Check, MS14-068 |
| 110 | POP3 mail service. Login via telnet or SSH? |
| 111 | RPCbind. This can help us look for NFS-shares |
| 119 | Network Time Protocol |
| 135 | MSRPC - Microsoft RPC |
| 139 | SMB Service. likely vulnerable to an SMB RCE |
| 161, 162 |SNMP Service |
| 389, 636 | LDAP Directory Service |
| 443 |HTTPS, check for HeartBleed? View certificate for information? |
| 445 |SMB Shares service, likely vulnerable to an SMB RCE |
| 587 |Submission. If Postfix is run on it, it could be vunerable to shellshock |
| 631 |CUPS. Basically a Linux Printer Service for sharing printers. |
| 1433 |Default MSSQL port. sqsh -S 10.1.11.41 -U sa |
| 1521 |Oracle DB. tnscmd10g version -h 10.1.11.51 |
| 2021 |Oracle XML DB. Check Default Passwords |
| 2049 | Network File System. showmount -e 10.1.11.64 |
| 3306 | MySQL Database. Connect: mysql --host=10.1.11.69 -u root -p |
| 3389 | Listening for RDP connection |
#### 

Nmap Enum Scripts[](#nmap-enum-scripts)

|#|Script|Type|Description|
|---|---|---|---|
|1|[smb-check-vulns.nse](https://github.com/mubix/tools/blob/master/nmap/scripts/smb-check-vulns.nse)|SMB|Scans for multiple SMB vulnerabilities.|
|2|[smb-vuln-cve2009-3103.nse](https://www.exploit-db.com/exploits/9594)|SMB|Windows Vista SP1/SP2 and Server 2008 (x86)|
|3|[smb-vuln-ms06-025.nse](https://www.exploit-db.com/exploits/1940)|SMB|Windows 2000 and Windows XP (x86)|
|4|[smb-vuln-ms07-029.nse](https://www.exploit-db.com/exploits/16366)|SMB|Windows 2003 SP1/SP2|
|5|[smb-vuln-ms08-067.nse](https://www.exploit-db.com/exploits/40279)|SMB|Windows XP|
|6|[smb-vuln-ms10-054.nse](https://www.exploit-db.com/exploits/14607)|SMB|XP, Vista, 7|
|7|[smb-vuln-ms10-061.nse](https://www.exploit-db.com/exploits/16361)|SMB|XP, Vista, 7|
|8|[smb-vuln-ms17-010.nse](https://www.exploit-db.com/exploits/42315)|SMB|EternalBlue. XP, Vista, 7, 8.1, 10|
|9|[smb-enum-shares.nse](https://github.com/nmap/nmap/blob/master/scripts/smb-enum-shares.nse)|SMB|Enumerates SMB Shares|
|10|[smb-enum-users.nse](https://github.com/nmap/nmap/blob/master/scripts/smb-enum-users.nse)|SMB|Attempts to enumerate Windows users|

_Example: Using an Nmap Script_
```
nmap -p 445 -vv --script=[script.nse] 10.10.10.10
```
### Linux Enumeration[](#linux-enumeration)
```
enum4linux -a 10.11.1.9
```
#### Banner Grabbing[](#banner-grabbing)

|#|Command|Description|
|---|---|---|
|1|/dev/tcp/$ip/$port|If nmap didn't banner grab or it's not installed.|

#### DNS Zone Transfer[](#dns-zone-transfer)

|#|Command|Description|
|---|---|---|
|1|`dig example.com any`|View DNS records on a domain.|
|2|`dnsrecon -d example.com`|Multiple queries to DNS server that enumerates DNS records.|

#### SMTP Email Enumeration[](#smtp-email-enumeration)

|#|Command|Description|
|---|---|---|
|1|`nmap -script smtp-commands.nse 10.11.1.41`|Scan for possible SMTP commands that can be executed|
|2|`smtp-user-enum -M VRFY -U /root/sectools/SecLists/Usernames/Names/names.txt -t 10.11.1.41`|SMTP Enum. `-M` for mode. `-U` for userlist. `-t` for target|