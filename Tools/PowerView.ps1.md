po
*copy saved in /home/kali/ActiveDirectory*

Allow scripting and then import powerview into memory:
```powershell
powershell -ep bypass
import-module .\Powerview.ps1
```

First run:
```powershell
get-netdomain
```

Get list all of all users in the domain and their attributes
```powershell
get-netuser

#helpful to pipe into select
get-netuser | select cn

		PS C:\tools> get-netuser | select cn
		
		cn
		--
		Administrator
		Guest
		krbtgt
		dave
		stephanie
		jeff
		jeffadmin
		iis_service
		pete
		jen

```

Get password properties for users:
```powershell
get-netuser | select cn,pwdlastset,lastlogon
```

Enumerate Groups:
```powershell
Get-NetGroup | select cn
```
```
Get-NetGroup "Sales Department" | select member
```

Get Domain Objects:
```powershell
Get-NetComputer | select operatingsystem,dnshostname
```

See members of a specific group:
```powershell
Get-NetGroup "Management Department" | select member
```

FIND LOCAL ADMIN 
*scans to see if current user has admin rights anywhere else in domain*
```powershell
Find-LocalAdminAccess
```

See if users are logged into other boxes:
```powershell
Get-NetSession -ComputerName web04 -verbose
```

See what users can actually view the above output
```powershell
Get-Acl -Path HKLM:SYSTEM\CurrentControlSet\Services\LanmanServer\DefaultSecurity\ | fl
```


See domain computers
```powershell
Get-NetComputer | select dnshostname,operatingsystem,operatingsystemversion
```

Enumerate Service Principal Names
```powershell
Get-NetUser -SPN | select samaccountname,serviceprincipalname
```
Then resolve that name:
```powershell
nslookup.exe web04.corp.com
```

Enumerate Access Control Entries with Powerview
```powershell
Get-ObjectAcl -Identity stephanie
```
*shows sids, which isnt helpful on its own, but you can convert them*
```powershell
Convert-SidToName S-1-5-21-1987370270-658905905-1781884369-553
```

Filter clean output for 'Generic All' access type (the highest most liberal type)
```powershell
Get-ObjectAcl -Identity "Management Department" | ? {$_.ActiveDirectoryRights -eq "GenericAll"} | select SecurityIdentifier,ActiveDirectoryRights
```

Convert multiple SIDs to names:
```powershell
"S-1-5-21-1987370270-658905905-1781884369-512","S-1-5-21-1987370270-658905905-1781884369-1104","S-1-5-32-548","S-1-5-18","S-1-5-21-1987370270-658905905-1781884369-519" | Convert-SidToName
```

Find Domain Shares:
```powershell
Find-Domainshare
```

check sysvol folder:
```powershell
ls \\dc1.corp.com\sysvol\corp.com\
```