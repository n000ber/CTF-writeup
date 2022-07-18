# TryHackMe (Gallery666)

## Challenge: 
![chall desc](img/gallery666/chall%20desc.jpg)
![chall desc pt 2](img/gallery666/chall%20desc%202.jpg)

## Solution: 
This challenge requires us to use SQLi to login as the admin account, upload a reverse shell and to privilege escalate to root through the use of nano.

Firstly, I started off with an nmap scan. From the nmap results, only ports 80 and 8080 were opened.

![nmap results](img/gallery666/nmap%20scan%20results.jpg)

Next, I headed over to the site hosted on port 80. However, it was the default Apache homepage.

![site hosted on port 80](img/gallery666/port%2080%20page.jpg)

Moving on, I decided to head to the site hosted on port 8080. I was then redirected to the login page for the content management system named `Simple Image Gallery System`.  

![simple img gallery cms](img/gallery666/port%208080%20page.jpg)

Next, I decided to try to use SQLi on the login form. I tried the payload `' OR 1=1-- -` and was able to login as the admin account.

![login as admin](img/gallery666/logged%20in%20as%20admin.jpg)

From the admin account, I headed over to the `albums` tab and then clicked on the `Avatars` album. From there, I was given the option to `upload` a file to the webserver. Then, I tried to upload the `php-reverse-shell.php` script from [pentestmonkey](https://pentestmonkey.net/tools/web-shells/php-reverse-shell)

![upload](img/gallery666/uploadedphp%20rev%20shell.jpg)

After uploading, I set up a netcat listener and opened the reverse shell upload in a new tab. 

![opened upload](img/gallery666/opened%20img%20in%20a%20new%20tab.jpg)

Nice! I've successfully obtained a reverse shell with the id `www-data`. Now, it's time for privilege escalation.

Since `user.txt` flag can only be opened by the user `mike`, I needed to escalate to that user. Running `linpeas.sh`, I found the following section very suspicious.

![sus output](img/gallery666/bash%20history%20pw.jpg)

Trying `mike` password as `b3stpassw0rdbr0xx`, I was able to switch to the user `mike`.

![successfully switched](img/gallery666/able%20to%20switch%20user%20to%20mike.jpg)

Next, I ran the command `sudo -l` on mike to list the allowed (and forbidden) commands for the user `mike`.

![sudo l mike](img/gallery666/sudo%20l%20mike.jpg)

Moving ahead, I decided to check what was the content of `/opt/rootkit.sh`.

![found content](img/gallery666/rootkit%20sh.jpg)

Basically, upon running the script, if the user's input is `read`, `nano` would run to display the file `/root/report.txt`.

Searching `nano` with `sudo` capabilities on gtfobins, I found the following.

![gtfobins](img/gallery666/sudo%20gtfobins.jpg)

After running the script using `sudo /bin/bash /opt/rootkit.sh` and putting in the input `read`, I was able to open up `nano`.

![Opened nano](img/gallery666/nano%20pops%20out.jpg)

Using the GTFObins guide of `CTRL-R`, `CTRL-X` and `reset; sh 1>&0 2>&0`, I managed to obtain a root shell.

![obtained a root shell](img/gallery666/root%20flag.jpg)

However, I was not done yet. I have yet to solve this part.

![havent solve](img/gallery666/still%20left%20with%20this%20qn.jpg)

Even though I could use SQLmap to solve it, I decided to login to the database and to dump out the necessary hash.

Firstly, I needed to go to the directory `/var/www/html` as it is the web root folder. From there, I could view that there is a directory called `gallery`. Inside that directory, I found `initialize.php`, which contains the database connection credentials.

![initialize](img/gallery666/initialize%20php.jpg)

Using the newly found credentials, I was able to login to the SQL DB using `mysql -h localhost -P 3306 -u gallery_user -p'passw0rd321' gallery_db`. From there, I was able to show the tables present in the `gallery_db` using the command `show tables;`. Finally, I was able to dump the users table, obtaining the administrator's hash.
> May need to stabilize shell first.

![Able to login to db](img/gallery666/tables%20shown.jpg)




