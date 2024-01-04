### Enumerate, shows if any NFS mount exposed:
```c
rpcinfo -p $ip

nmap $ip --script=msrpc-enum

nmap -n -v -sV -Pn 192.168.0.101 --script=msrpc-enum

```

### Connection

Connect to an RPC share without a username and password and enumerate privledges

```c
rpcclient --user="" --command=enumprivs -N $ip
```

Connect to an RPC share with a username and enumerate privledges

```c
rpcclient --user="<Username>" --command=enumprivs $ip
```

### Command
```c
rpcclient>srvinfo

rpcclient>enumdomusers

rpcclient>getdompwinfo

```
