![[Pasted image 20231219192851.png]]Web server on 80 shows this page. When I try to process the code 'id' burp says it redirects to a page that doesnt exist. So lets change the burprequest to post to this same page.

When doing that id, works. Now do a callback and get www-data shell. Priv Esc is just a cron job running the script in the /var/www folder named backup.sh