Harvester will pull OSINT information such as emails, subdomains, any DNS information
- First step is create a file like sources.txt
- Nano sources.txt
- baidu
bufferoverun
crtsh
hackertarget
otx
projecdiscovery
rapiddns
sublist3r
threatcrowd
trello
urlscan
vhost
virustotal
zoomeye

-```shell-session
cat sources.txt | while read source; do theHarvester -d "${TARGET}" -b $source -f "${source}_${TARGET}";done
```

- If you want to sort the results, you can run the following:
- ```shell-session
cat *.json | jq -r '.hosts[]' 2>/dev/null | cut -d':' -f 1 | sort -u > "${TARGET}_theHarvester.txt"
```