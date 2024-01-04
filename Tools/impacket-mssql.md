```c
impacket-mssqlclient -port 1433 oscp.exam/sql_svc:Dolphin1@10.10.94.148 
-windows-auth
```

```c
impacket-mssqlclient oscp.exam/sa:<password>@<target_ip>
-windows-auth -local-auth
```