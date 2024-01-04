- Operating System information and version
	- cat /etc/os-release
- Kernel version
	- uname -a
	- cat /proc/version
- System shell enumeration
	- cat /etc/shells
- Drives and share enumeration
	- lsblk
- Viewing network routing table
	- route
	- netstat -rn
- User Enumeration
	- cat /etc/passwd |  cut -f1 -d:
	- getent group sudo will show members of any interesting groups
- All Hidden Files
	- find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep <username>
- All Hidden Directories
		- find / -type d -name ".*" -ls 2>/dev/null

- When all else fails, you can upload linPEAS to do manual walkthrough escalation