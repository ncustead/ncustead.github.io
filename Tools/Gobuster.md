#### Gobuster

```c
-e    // extended mode that renders the full url
-k    // skip ssl certificate validation
-r    // follow cedirects
-s    // status codes
-b    // exclude status codes
-k            // ignore certificates
--wildcard    // set wildcard option

$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u http://<RHOST>/
$ gobuster dir -w /usr/share/seclists/Discovery/Web-Content/big.txt -u http://<RHOST>/ -x php
$ gobuster dir -w /usr/share/wordlists/dirb/big.txt -u http://<RHOST>/ -x php,txt,html,js -e -s 200
$ gobuster dir -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u https://<RHOST>:<RPORT>/ -b 200 -k --wildcard
```

##### [](https://github.com/0xsyr0/OSCP#common-file-extensions)Common File Extensions

```c
txt,bak,php,html,js,asp,aspx
```

##### [](https://github.com/0xsyr0/OSCP#common-picture-extensions)Common Picture Extensions

```c
png,jpg,jpeg,gif,bmp
```

##### [](https://github.com/0xsyr0/OSCP#post-requests)POST Requests

```c
gobuster dir -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -u http://<RHOST>/api/ -e -s 200
```

##### [](https://github.com/0xsyr0/OSCP#dns-recon)DNS Recon

```c
gobuster dns -d <RHOST> -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
gobuster dns -d <RHOST> -t 50 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt
```

##### [](https://github.com/0xsyr0/OSCP#vhost-discovery)VHost Discovery

```c
gobuster vhost -u <RHOST> -t 50 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt
gobuster vhost -u <RHOST> -t 50 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

##### [](https://github.com/0xsyr0/OSCP#specifiy-user-agent)Specifiy User Agent

```c
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
```


+ **We will begin with Gobuster.**  
```c
export URL="https://example.com/"  
```
  
+ **Here are my localized commands:**  
**BUST DIRECTORIES:**  
```c
gobuster dir -u $URL -w /opt/SecLists/Discovery/Web-Content/raft-medium-directories.txt -k -t 30 
```
  
**BUST** **FILES:**  
```c
gobuster dir -u $URL -w /opt/SecLists/Discovery/Web-Content/raft-medium-files.txt -k -t 30  
```
  
**BUST SUB-DOMAINS:**  
```c
gobuster dns -d someDomain.com -w /opt/SecLists/Discovery/DNS/subdomains-top1million-110000.txt -t 30  
```
**-->** _Make sure any DNS name you find resolves to an in-scope address before you test it_.