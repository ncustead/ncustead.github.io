
export URL="https://example.com/**FUZZ**"  
  
**FUZZ DIRECTORIES:**  
```c
export URL="https://example.com/**FUZZ/**"  
wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/raft-medium-directories.txt --hc 404 "$URL"  
```
  
**FUZZ FILES:**  
```c
wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/raft-medium-files.txt --hc 404 "$URL"  
```

**AUTHENTICATED FUZZING:**  
```c
wfuzz -c -b "<SESSIONVARIABLE>=<SESSIONVALUE>" -z file,/opt/SecLists/Discovery/Web-Content/raft-medium-files.txt --hc 404 "$URL"  
```

  
**FUZZ DATA AND CHECK FOR PARAMETERS:**  
```c
export URL="https://example.com/?parameter=**FUZZ**  
```
**-->** and/or some combination of...  
```c
export URL="https://example.com/?**FUZZ**=data  
wfuzz -c -z file,/opt/SecLists/Discovery/Web-Content/burp-parameter-names.txt "$URL"  
```
  
+ **Can I FUZZ Post Data?**  
**-->** Yup.  
**-->** Example of Command Injection **POST Checks**:  
```c
wfuzz -c -z file,/usr/share/wordlists/Fuzzing/command-injection.txt -d "postParameter=**FUZZ**" "$URL"
```