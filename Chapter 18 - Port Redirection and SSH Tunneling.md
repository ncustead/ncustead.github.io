## Port Forwarding with Socat
```
socat -ddd TCP-LISTEN:2345,fork TCP:10.4.219.215:5432
```

python3 -c 'import pty; pty.spawn("/bin/bash")'

ssh -N -R 127.0.0.1:2345:10.4.226.215:5432 kali@192.168.45.160
## SSH Tunneling

## Exercises To-Do

- [ ] 2.1.1 (page 20)