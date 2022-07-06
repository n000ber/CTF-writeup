# Burpsuite Academy (Referer-based Access Control)

## Challenge: 

![challenge description](referer_access_control/chall%20desc.png)

## Solution:
This challenge requires us to use an unprivileged account (wiener) to promote itself to become an administrator.

To start off, we visit the website. 

![website visit](referer_access_control/webpage%201.png)

From there, we could click on `My account` and login with the known administrator account credentials of `administrator:admin`.

![login as admin](referer_access_control/login%20as%20admin.png)

Afterwards, we tried to upgrade Carlos' privilege using the admin panel.

![admin panel](referer_access_control/admin%20panel.png)

![upgrading carlos privilege](referer_access_control/upgrade%20carlos%20privilege.png)

From the Burpsuite proxy, we could see that the `Referer` header was set to the `/admin` directory of the webpage. 

Next, we visit Wiener's account. From Wiener's account, we were able to obtain his cookie. 

![wiener cookie](referer_access_control/wiener%20cookie.png)

Editing the upgrading account request username to wiener and changing the cookie to that of Wiener, we were able to successfully send the request.

![success](referer_access_control/wiener%20upgrade.png)

We have successfully completed this lab! :')