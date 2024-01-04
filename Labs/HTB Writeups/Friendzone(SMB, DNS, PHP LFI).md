- I started off running nmap automator to get a full scan of the target IP
- ![[Pasted image 20231121132210.png]]
- One thing that sticks out for the initial scan is the smb details, leading me to try and use smbclient![[Pasted image 20231121132136.png]]
- Another item is DNS TCP which can be used to attempt zonetransfer to get more vhost/subdomain information
- I start off with smbclient as follows in the screenshot![[Pasted image 20231121132702.png]]
- I was not able to authenticate on Files or Development using the smb creds text file I found. SSH, FTP, and SMB did not let me auth. Next step is find a login page to figure out how and where to put the creds towards
- I used Dirbuster on both http and https to try and find additional hidden web pages
![[Pasted image 20231121135606.png]]
![[Pasted image 20231121140226.png]]
Only thing that worked was the /js page which had a file in it. I went back to dig to try and get some zone transfers which I was able to do
![[Pasted image 20231121141040.png]]
From the HTTP page, we can see that there is a friendzoneportal and a friendzone so we can dig on both of those and add to our /etc/hosts file
![[Pasted image 20231121141220.png]]
After gathering all the DNS info, we visit the pages in our hosts file. I get two different logins for the administrator labelled pages
![[Pasted image 20231121141734.png]]![[Pasted image 20231121142000.png]]
Knowing this is a php page and I can most likely mess with the URL for LFI inclusion based off of this scan.
![[Pasted image 20231121150310.png]]
There is an upload.php that was not found on the gobuster which makes me think that I can mess around that idea.
Going back to the above screenshot with the pagename parameter, we can assume that parameter is vulnerable to LFI
![[Pasted image 20231121150711.png]]
I put a cmd.php into the Dev Folder I could access with Admin on SMB. If I visit the URL, can now execute commands through the URL
![[Pasted image 20231121151153.png]]
