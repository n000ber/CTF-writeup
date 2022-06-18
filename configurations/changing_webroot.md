# Configurations (Changing XAMPP Webroot)

## Challenge: 

To change the XAMPP webroot to `C:\Users\WQ\Documents\WEBSITES`. 

## Solution:

The solution is largely based on this Stackoverflow [link](https://stackoverflow.com/questions/18902887/how-to-configuring-a-xampp-web-server-for-different-root-directory).

Firstly, go to the Apache Control Panel and click on the `httpd.conf` file. 

![http.conf](img/changing_webroot/httpd%20conf.png)

Next, use `Ctrl-F` to search for `htdocs`.

![search htdocs](img/changing_webroot/search%20htdocs.png)

Replace the following `DocumentRoot` and `<Directory>` values to your preferred directory.

![replaced](img/changing_webroot/replaced.png)

If you do not have virtual host configurations in `httpd-xampp.conf`, you have successfully completed the steps to change the XAMPP webroot. Else, read on.

Next, open up `httpd-xampp.conf` from the Apache Control Panel.

![open up httpd-xampp conf](img/changing_webroot/edit%20httpd%20xampp%20config.png)

Search for htdocs and replace the values in `DocumentRoot` to your site name.

![replace values2](img/changing_webroot/search%20htdocs2.png)
![replaced](img/changing_webroot/edited%20docroot.png).

After saving the files, restart Apache server and restart your browser. Your page should load from the specified webroot.

![success](img/changing_webroot/able%20to%20view%20our%20site%20.png)

