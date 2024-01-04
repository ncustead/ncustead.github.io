## Use KPCLI to access Keepass databases

first, you may need to crack it with:
```c
keepass2john Database.kdbx > newhash
```

Then, edit that hash to take the Database part out![[Pasted image 20231123095814.png]]
This should now  look like:
```c
$keepass$*2*60*0*ed890395c5503e50453897e48fd2d79ece2ae3466b51b6fb941cd413f5c89b43*3edacb91f15bae05d3fd546f201cd8924676b662f6101ba57155e0f4aeae9b61*7a963146ec300519645fbc90ca4e258d*90939579da95cd23a9c90aef5a7a507d7c9ee647ed47c0fa05729a1262d7d73e*e97f9fe2f7a1efe24b054dfcb47e8edab5dd7eb96c5731f32e64a9d3a1db5dcf
```

this should now be crackable to give a password with hashcat:
```c
hashcat -m 13400 newhash /usr/share/wordlists/rockyou.txt
```

![[Pasted image 20231123100125.png]]

Access the Database.kdbx with kpcli:
```c
kpcli --kdb=/home/kali/Database.kdbx 
```

Browse thru, 
![[Pasted image 20231123100238.png]]

You 'cd' into each of these groups or files, e.g. 
```c
cd Database
```

![[Pasted image 20231123100346.png]]