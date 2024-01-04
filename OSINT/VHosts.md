Vhosts iare essentially subdomains, for example: admin.target.com or dev.target.com that can potentially lead to hidden login pages

- Good list to use could be found in /usr/share/seclists/discovery/DNS/namelist.txt
- You can cp the namelist .txt into a file called vhosts
- Fuzz the website to see if any of the names show a 612 success code
- ffuf -w ./vhosts -u http://<target IP> -H "HOST: FUZZ.<target.com>" -fs 612