# UAC Bypass

https://book.hacktricks.xyz/windows-hardening/authentication-credentials-uac-and-efs/uac-user-account-control

https://gist.github.com/netbiosX/a114f8822eb20b115e33db55deee6692

#### Check UAC

To confirm if UAC is enabled do:

```c
REG QUERY HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v EnableLUA
```

HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System

EnableLUA REG\_DWORD 0x1

If it's `**1**` then UAC is **activated**, if its `**0**` or it **doesn't exist**, then UAC is **inactive**.

Then, check **which level** is configured:

REG QUERY HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System\ /v ConsentPromptBehaviorAdmin

HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System

ConsentPromptBehaviorAdmin REG\_DWORD 0x5

* If `**0**` then, UAC won't prompt (like **disabled**)
* If `**1**` the admin is **asked for username and password** to execute the binary with high rights (on Secure Desktop)
* If `**2**` (**Always notify me**) UAC will always ask for confirmation to the administrator when he tries to execute something with high privileges (on Secure Desktop)
* If `**3**` like `1` but not necessary on Secure Desktop
* If `**4**` like `2` but not necessary on Secure Desktop
* if `**5**`(**default**) it will ask the administrator to confirm to run non Windows binaries with high privileges

Then, you have to take a look at the value of `**LocalAccountTokenFilterPolicy**` If the value is `**0**`, then, only the **RID 500** user (**built-in Administrator**) is able to perform **admin tasks without UAC**, and if its `1`, **all accounts inside "Administrators"** group can do them.

And, finally take a look at the value of the key `**FilterAdministratorToken**` If `**0**`(default), the **built-in Administrator account can** do remote administration tasks and if `**1**` the built-in account Administrator **cannot** do remote administration tasks, unless `LocalAccountTokenFilterPolicy` is set to `1`.



Summary

* If `EnableLUA=0` or **doesn't exist**, **no UAC for anyone**
* If `EnableLua=1` and `**LocalAccountTokenFilterPolicy=1**` **, No UAC for anyone**
* If `EnableLua=1` and `**LocalAccountTokenFilterPolicy=0**` **and** `**FilterAdministratorToken=0**`**, No UAC for RID 500 (Built-in Administrator)**
* If `EnableLua=1` and `**LocalAccountTokenFilterPolicy=0**` **and** `**FilterAdministratorToken=1**`**, UAC for everyone**

All this information can be gathered using the **metasploit** module: `post/windows/gather/win_privs`

You can also check the groups of your user and get the integrity level:

net user %username%

whoami /groups | findstr Level
