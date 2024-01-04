## Manual Enumeration

#### Windows Tools

net.exe

```
#get domain users
net user /domain

#get information about a specific domain user
net user jeffadmin 

#see all domain groups
net group /domain

#see local groups
net localgroup 

#get domain group information about a specific domain group
net group "Management Department" 

```


#### Powershell enumeration

```
#return domain object for current user
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()

#first, enable scripts
powershell -ep bypass

#Get domain name easily
([adsi]'').distinguishedName

```

LDAP ENUMERATION SCRIPT
*write to powershell ise, which will create full ldap path required for enumeration*
```
$domainObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = $domainObj.PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName



$PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
$DN = ([adsi]'').distinguishedName 
$LDAP = "LDAP://$PDC/$DN"

$direntry = New-Object System.DirectoryServices.DirectoryEntry($LDAP)

$dirsearcher = New-Object System.DirectoryServices.DirectorySearcher($direntry)
$dirsearcher.filter="samAccountType=805306368"
$result = $dirsearcher.FindAll()

Foreach($obj in $result)
{
    Foreach($prop in $obj.Properties)
    {
        $prop
    }

    Write-Host "-------------------------------"
}
```

Import-Module .\function.ps1

use:
```
LDAPSearch -LDAPQuery "(samAccountType=805306368)"

#or....

LDAPSearch -LDAPQuery "(objectclass=group)"

#or....

foreach ($group in $(LDAPSearch -LDAPQuery "(objectCategory=group)")) {
>> $group.properties | select {$_.cn}, {$_.member}
>> }

#or....

$sales = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Sales Department))"

#or....

$sales.properties.member


$group = LDAPSearch -LDAPQuery "(&(objectCategory=group)(cn=Development Department*))"

$group.properties.member
##if you find a group member you want attributes for:
	 $michelle = LDAPSearch -LDAPQuery "(&(objectCategory=user)(cn=michelle*))"


$michelle.properties

```

Source code:
```
function LDAPSearch {
    param (
        [string]$LDAPQuery
    )

    $PDC = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain().PdcRoleOwner.Name
    $DistinguishedName = ([adsi]'').distinguishedName

    $DirectoryEntry = New-Object System.DirectoryServices.DirectoryEntry("LDAP://$PDC/$DistinguishedName")

    $DirectorySearcher = New-Object System.DirectoryServices.DirectorySearcher($DirectoryEntry, $LDAPQuery)

    return $DirectorySearcher.FindAll()

}
```

## Automated Enumeration

## Enumeration through Service Principal Names

Enumerate SPNs, on windows by default:
```
setspn -L iis_service
```


## Active Directory Permission Types

```
GenericAll: Full permissions on object
GenericWrite: Edit certain attributes on the object
WriteOwner: Change ownership of the object
WriteDACL: Edit ACE's applied to object
AllExtendedRights: Change password, reset password, etc.
ForceChangePassword: Password change for object
Self (Self-Membership): Add ourselves to for example a group
```


Add Stephanie, if she has all access (generic access) to domain
```
 net group "Management Department" stephanie /add /domain
```

## Domain Shares

```
Find-Domainshare
```
## Exercises To-Do

- [ ] 2.1.1 (page 20)