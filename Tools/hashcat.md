
You extracted the MD5 hash "19adc0e8921336d08502c039dc297ff8" from a target system. Create a rule which makes all letters upper case and duplicates the passwords contained in rockyou.txt and crack the hash.
```c
hashcat -m 0 crackme2.txt /usr/share/wordlists/rockyou.txt -r demo2.rule --force
```
```c
BUTTERFLY5BUTTERFLY5
```
**Duplicates the words in the word list and made them all uppercase

You extracted the MD5 hash "056df33e47082c77148dba529212d50a" from a target system. Create a rule to add "1@3$5" to each password of the rockyou.txt wordlist and crack the hash.
```c
hashcat -m 0 crackme.txt -w /usr/share/wordlists/rockyou.txt -r demo.rule --force
```
```c
$1 $@ $3 $$ $5 #APPENDS ALL THESE CHARACHTERS TO EACH WORD
```
```c
courtney1@3$5 #RESULT
```

### HASH IDENTIFIER
	Gives hash function plus what the value for hashcat would be (e.g. what to put after -m in a hashcast command)
```c
hashid 4a41e0fdfb57173f8156f58e49628968a8ba782d0cd251c6f3e2426cb36ced3b647bf83057dabeaffe1475d16e7f62b7 -m
```

Cracking a keepass hash file
```c
hashcat -m 13400 keepass.hash /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule --force
```

##### Cracking ASPREPRoast Password File

```c
hashcat -m 18200 -a 0 <FILE> <FILE>
```

##### [](https://github.com/0xsyr0/OSCP#cracking-kerberoasting-password-file)Cracking Kerberoasting Password File

```c
hashcat -m 13100 --force <FILE> <FILE>
```

##### [](https://github.com/0xsyr0/OSCP#bruteforce-based-on-the-pattern)Bruteforce based on the Pattern

```c
hashcat -a3 -m0 mantas?d?d?d?u?u?u --force --potfile-disable --stdout
```

##### [](https://github.com/0xsyr0/OSCP#generate-password-candidates-wordlist--pattern)Generate Password Candidates: Wordlist + Pattern

```c
hashcat -a6 -m0 "e99a18c428cb38d5f260853678922e03" yourPassword|/usr/share/wo
```
