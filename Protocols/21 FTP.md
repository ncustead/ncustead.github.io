When extended passive isnt working.,..
```c
wget -m --no-passive ftp://anonymous:anonymous@10.10.10.98 #Download all
```
```
# Version detection + NSE scripts
nmap -Pn -sV -p 21 --script="banner,(ftp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "tcp_21_ftp_nmap.txt" $ip
```