![[Pasted image 20230823155252.png]]
- After getting the password for Stuart, I ssh'd in with his creds.
- I uploaded linPEAS to find any vulnerable pathways to root this machine.
- This led to /opt directory where there were backup zip files.
- SCP -R stuart@IP:/opt/backup /home/tony/zips
- zip2john -> led to code blue
- use that password to unlock the zip
- zip contains a secret string with the user chloe, so I was able to su to chloe
- chloe has sudo (all | all) so she could sudo su root