To create a pingsweep script for any Linux (Internal or External)
```c
 #!/bin/bash
 ping sweep
 for i in {0..254}; do ping -c 1
 10.10.110.$i; done
```
- Example would be ./pingsweep.sh > results.txt
- Grep "bytes from" results.txt

If you want to run it from Linux cmd line, you can run:
- for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done

 for i in {1..254} ;do (ping -c 1 10.0.0.$i | grep "bytes from" &) ;done

 ### To run a powershell ping sweep, execute
 ```c
 1..254 \| % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}`|
```
