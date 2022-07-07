# Burpsuite Academy (User ID controlled by request parameter with data leakage in redirect)

## Challenge: 

![challenge description](img/userid_access_control/chall%20desc.png)

## Solution:
This challenge requires us to exploit an access control vulnerability to obtain Carlos's API key.

To start off, we login to wiener's account using the provided credentials.

![login as wiener](img/user_role_mod/login%20page.png)

Next, using Burpsuite Proxy, we obtained the following request.

![request](img/userid_access_control/login%20request.png)

Moving on, we decided to change the `id` parameter in the GET request to become `carlos`. With that, we were able to obtain carlos' API key.

![obtained carlos api](img/userid_access_control/got%20api%20key.png)

With that, we have successfully completed this lab!

![completed lab](img/userid_access_control/solved.png)