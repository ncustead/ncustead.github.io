**nitial Foothold:**

Beginning the initial nmap enumeration.

![](https://miro.medium.com/v2/resize:fit:700/1*F6Ntuc7hSkt48zX3T6yx1w.png)

Running default nmap scripts.

![](https://miro.medium.com/v2/resize:fit:700/1*1OuYe0GGfKl6jsn3CKr5KA.png)

The ftp service has anonymous logins allowed. Enumerating the shared contents. Unfortunately we cannot read any of the shared content. The other ftp service doesn’t permit anonymous login.

Enumerating the web service.

![](https://miro.medium.com/v2/resize:fit:700/1*bvBOcd9YLXrGoBF2T2LGjA.png)

I tried a few default credentials but they don’t work. I’ll try brute forcing the ftp services to find any other credentials.

![](https://miro.medium.com/v2/resize:fit:700/1*UBIYYENkJnyIp1GOGjExgw.png)

Using this to login into the ftp server. I see two interesting files, one of which contains a set of credentials.

![](https://miro.medium.com/v2/resize:fit:700/1*TtxBW-mYCY3MHTgTyK5EMA.png)

It’s a MD5 hash. Attempting to crack it using john.

![](https://miro.medium.com/v2/resize:fit:700/1*BFUlF1yOe0wN3p2ogAcKMg.png)

![](https://miro.medium.com/v2/resize:fit:700/1*KyEJ11b1GqlZiKP5VWuBAw.png)

Using the credentials to login into the web service running on port 242.

![](https://miro.medium.com/v2/resize:fit:700/1*Otp-ndYlYFRj9HQQ0s8rew.png)

We can see the same index.php file on this webpage. This means that we can write a reverseshell, upload it on the same folder on the ftp server and trigger it with this web service.

Since this is a windows box, we generate a reverseshell using msfvenom, and create two scripts. The first one uploads the executable file onto the machine from our locally running python web server. The second one triggers the executable to give us a reverse shell.

![](https://miro.medium.com/v2/resize:fit:700/1*Z-L1crlajSU1F_4wPBlcyA.png)

Uploading it onto the ftp server.

![](https://miro.medium.com/v2/resize:fit:700/1*1CIUe3nFk8-HZwF_u2mvnQ.png)

Triggering the upload payload.

![](https://miro.medium.com/v2/resize:fit:700/1*E078XIcatghOu3deAdgKqA.png)

![](https://miro.medium.com/v2/resize:fit:700/1*cVK5x7_TKpLfbGBKdunWSg.png)

Setting up the listener and running the run payload. We get back a reverse shell.

![](https://miro.medium.com/v2/resize:fit:700/1*0CypXfsV12afsrDyHeXf3w.png)

We can find the first key under C:\Users\apache\Desktop

![](https://miro.medium.com/v2/resize:fit:700/1*JZecjYrJdq4xd1kxVWy_cA.png)

**Privilege Escalation:**

![](https://miro.medium.com/v2/resize:fit:700/1*Pft1cW2BEYcwnqlPNglBug.png)

It’s a x86 Windows 2008 server box with no installed patches. There a number of exploits that can be used to gain privilege escalation. A useful link one must have is [https://github.com/SecWiki/windows-kernel-exploits](https://github.com/SecWiki/windows-kernel-exploits).

I download the exploit and paste it into the same ftp server.

![](https://miro.medium.com/v2/resize:fit:700/1*UI6J3LssF_fVeYJ5VXJauw.png)

It works perfectly and we get a reverse shell with system privileges.

![](https://miro.medium.com/v2/resize:fit:700/1*2RPqJG9SSUfk3KAdZvh4sA.png)