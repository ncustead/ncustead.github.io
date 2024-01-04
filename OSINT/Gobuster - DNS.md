Gobuster can also be used to find out any DNS information for zone transfers along with brute forcing

-export TARGET="<target Domain>"
-export NS="<target NS that came back from dig or nslookup>"
-export WORDLIST="<path to wordlist to use for DNS>"
-gobuster dns -q -r "${NS}" -d "${TARGET}" -w "${WORDLIST}" -p ./patterns.txt -o "gobuster_${TARGET}.txt"