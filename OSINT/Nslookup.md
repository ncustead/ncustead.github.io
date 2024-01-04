Nslookup is a way to try and identify nameservers or do zone transfers:
- nslookup -type=NS <domain name target>
- nslookup -type=any -query=AXFR <domain name target>