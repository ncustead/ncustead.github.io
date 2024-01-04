After getting a valid user login, go through the dashboard
- Go to appearance in the dashboard tabs
- Make sure to see which theme is active
- Go to Editor, and edit a non critical file such as 404.php
- Copy and paste a php reverse shell, overwrite the script.
- Go back and make the theme you edited to become the active theme
- Make sure to set up a listener on AP with the specified port in the php shell 
- In the URL, visit the <IP address or hostname>/wordpress/wp-content/themes/<edited theme>/404.php
