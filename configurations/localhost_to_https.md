# Configurations (Localhost to have HTTPS)

## Challenge: 

Serving the site "accounts-google.com" locally with XAMPP and allowing it to have HTTPS.

![results](img/localhost_to_https/https%20for%20accounts-google.png)

## Solution:

The solution is largely based on this Stackoverflow [link](https://stackoverflow.com/questions/64800565/how-to-create-valid-ssl-in-localhost-for-xampp). 

Firstly, open File Explorer and head to `C:\xampp\apache` and create a folder named `crt`.

![create crt folder](img/localhost_to_https/create%20the%20crt%20folder.png)

Secondly, open up CMD at `C:\xampp\apache\crt` directory and download [cert.conf](https://gist.githubusercontent.com/turtlepod/3b8d8d0eef29de019951aa9d9dcba546/raw/518d3a96b7bb03494ada4f2ebde8325fb6ba6966/cert.conf) and [make-cert.bat](https://gist.githubusercontent.com/turtlepod/e94928cddbfc46cfbaf8c3e5856577d0/raw/7742d25246d0617629b23cc6fe7eabbd89078fa6/make-cert.bat)

![download cert.conf](img/localhost_to_https/downloading%20cert%20conf.png)
![download make cert bat](img/localhost_to_https/downloading%20make%20cert%20bat.png)

> You can install Wget via the command `choco install wget`. Check out how to install Chocolatey [here](https://chocolatey.org/install).

Moving on, open up the cert.conf using the command `notepad cert.conf`. Substitute the `{{domain}}` variables to your site name. Do remember to save your changes.

![replacing with sitename](img/localhost_to_https/changed%20domain%20to%20yr%20site.png)

Afterwards, run the make-cert.bat file, and do input your site name when prompted.

![run make-cert.bat](img/localhost_to_https/run%20make%20cert%20bat.png)

Next, a folder named after your site name would appear in the `C:\xampp\apache\crt` directory. After heading to the folder, double click the server.crt file.

![run server.crt](img/localhost_to_https/double%20click%20server%20crt.png)

Do click on the `Install Certificate` option and select `Local Machine` when prompted.

![installing cert](img/localhost_to_https/installing%20cert.png)

Furthermore, do place all certificates in the `Trusted Root Certification Authorities` as shown below.

![place in trca](img/localhost_to_https/place%20in%20trusted%20root%20cert%20auth.png)

After completing all the steps, restart your XAMPP server and restart your browser. Now, you would be able to view your locally served site with HTTPS.

![HTTPS](img/localhost_to_https/https%20for%20accounts-google.png)