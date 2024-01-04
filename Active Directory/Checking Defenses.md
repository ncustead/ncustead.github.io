- Firewalls
	- Checking for firewalls
		- netsh advfirewall show allprofiles - PowerShell
		- sc query windefend - Windows CMD
- Status and Configuration Status
	- Get-MpComputerStatus - shows AV if enabled, what type, if on-demand threat alerting on, etc.


- Logged in users
	- qwinsta - PowerShell cmd to check for logged in sessions
	- ```shell-session
ldapsearch -h 172.16.5.5 -x -b "DC=INLANEFREIGHT,DC=LOCAL" -s sub "*" | grep -m 1 -B 10 pwdHistoryLength
```


- Network Information
 
|**Networking Commands**|**Description**|
|---|---|
|`arp -a`|Lists all known hosts stored in the arp table.|
|`ipconfig /all`|Prints out adapter settings for the host. We can figure out the network segment from here.|
|`route print`|Displays the routing table (IPv4 & IPv6) identifying known networks and layer three routes shared with the host.|
|`netsh advfirewall show state`|Displays the status of the host's firewall. We can determine if it is active and filtering traffic.|