- You can use LDAP filtering to further enumerate any users if you need to: 
	- dsquery * -filter "(&(objectCategory=user)(userAccountControl:1.2.840.113556.1.4.803:=2)(memberOf:1.2.840.113556.1.4.1941:=CN=Administrators,CN=Builtin,DC=INLANEFREIGHT,DC=local))" -attr samAccountName description

	- This query was used to find any disable accounts with administrative privileges
