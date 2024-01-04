
### How to Crack a Zip File Password

Finally, let's crack a zip file password. To do that, we first have to get the hash of the zip file’s password.

Like unshadow, John has another utility called zip2john. zip2john helps us to get the hash from zip files. If you are cracking a .rar file, you can use the rar2john utility.

Here is the syntax to get the password hash of a zip file:

```c
$ zip2john file.zip > zip.hashes
```

The above command will get the hash from the zip file and store it in the zip.hashes file. You can then use John to crack the hash.

```c
$john zip.hashes
```

### How to Crack a Linux Password

Here is how we use the unshadow command:

```c
$ unshadow /etc/passwd /etc/shadow > output.db
```

This command will combine the files together and create an output.db file. We can now crack the output.db file using John.

```c
$ john output.db
```


### How to Crack a Windows Password

Let's start with Windows. In Windows, the password hashes are stored in the [SAM database](https://en.wikipedia.org/wiki/Security_Account_Manager). SAM uses the LM/NTLM hash format for passwords, so we will be using John to crack one.

Getting passwords from the SAM database is out of scope for this article, but let's assume you have acquired a password hash for a Windows user.

Here is the command to crack it:

```c
$ john --format=lm crack.txt
```