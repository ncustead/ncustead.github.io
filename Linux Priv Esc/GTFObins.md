- Binaries on the victim nix system that can be exploited to gain system level access
	- Following is a one liner script that compares binaries on the nix victim box to what is on the website repo
	- ``` for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done