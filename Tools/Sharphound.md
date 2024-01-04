first, import sharphound.ps1
```c
import-module .\sharphound.ps1
```

Get help:
```c
Get-Help Invoke-BloodHound
```

MUST FIRST DO THIS:
```c
Invoke-BloodHound -CollectionMethod All -OutputDirectory C:\Users\stephanie\Desktop\ -OutputPrefix "corp audit"
```