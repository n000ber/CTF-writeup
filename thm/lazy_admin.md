# TryHackMe (LazyAdmin)

## Challenge: 
![chall desc](img/lazyadmin/chall%20desc.jpg)

## Solution:
This challenge requires us to gain RCE on SweetRice CMS v1.5.1, followed by privilege escalation using incorrect file permissions.

To start off, I used Threader3000 to scan the host, finding ports 22 and 80 to be open.

![threader3000](img/lazyadmin/threader3000.jpg)

Next, I ran the relevant Nmap commands to further enumerate the open ports.
![ports enumeration](img/lazyadmin/nmap%20results.jpg)

Following that, I ran `dirsearch -u 10.10.83.151 -X -x 400,500 -r -f -t 100 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt` and found several subdomains such as `/content`.

![dirsearch](img/lazyadmin/dirsearch%20results.jpg)

Next, I enumerated the `/content` subdomain even further.

![enum content](img/lazyadmin/further%20dirsearch.jpg)

Then, I visited the `/content/as` and `/content/inc` subdomains. `/content/as` seems to be the login page for SweetRice CMS. `/content/inc` seems to contain numerous website files.

![content as](img/lazyadmin/content%20as.jpg)

![content inc](img/lazyadmin/content%20inc.jpg)

Upon further enumeration inside the `/content/inc` subdomain, I found an SQL backup file.

![sql backup](img/lazyadmin/mysql%20backup.jpg)

Opening the backup file, I found the username `manager` and the password hash.

![found creds](img/lazyadmin/creds.jpg)

Next, I pasted the hash into [a MD5 reversing site](https://md5.gromweb.com/) and found the password to be `Password123`.

![cracked pass](img/lazyadmin/online%20reversed.jpg)

Moving on, I managed to login to the `/content/as` page.

![welcome](img/lazyadmin/welcome%20sweetrice.jpg)

Next, I decided to use the `theme` tab to send in my PHP reverse shell.

![theme](img/lazyadmin/modify%20themes.jpg)

For the PHP reverse shell, I used the one provided by Pentest Monkey and uploaded the zipped PHP reverse shell file.

![zipped](img/lazyadmin/zip%20the%20rev%20shell.jpg)

Next, I went to `/content/_themes` to view the uploaded reverse shell.

![themes](img/lazyadmin/themes%20folder.jpg)

From there, I was able to gain a reverse shell.

![rev shell](img/lazyadmin/obtained%20rev%20shell.jpg)

![rev shell user](img/lazyadmin/user%20flag.jpg)

For privilege escalation, I decided to use `sudo -l` to list the allowed (and forbidden) commands for the invoking user.

![sudo l](img/lazyadmin/sudo%20l.jpg)

The `/etc/copy.sh` file is a reverse shell by [Pentest Monkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet). 

![pentest monkey rev shell](img/lazyadmin/etc%20copy%20sh.jpg)

Next, I viewed the file permissions for the `/etc/copy.sh` file and found that the `www-data` user have write permissions.

![permissions](img/lazyadmin/incorrect%20perm.jpg)

Moving on, I edited the IP address to my attacking IP and was able to gain a reverse shell as the root user.

![gen rev shell](img/lazyadmin/generating%20rev%20shell.jpg)

![root shell](img/lazyadmin/root%20shell.jpg)

![root flag](img/lazyadmin/root%20flag.jpg)
