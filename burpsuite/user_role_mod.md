# Burpsuite Academy (User role can be modified in user profile)

## Challenge: 

![challenge description](img/user_role_mod/chall%20desc.png)

## Solution:
This challenge requires us to use the update email function to update the roleid to 2, to be able to access the admin page.

Firstly, we log in using the provided credentials `wiener:peter`. 

![login](img/user_role_mod/login%20page.png)

Secondly, we were able to use the update email function to update Wiener's email address.

![update email](img/user_role_mod/input%20email.png)

Next, using Burpsuite, we saw that the request was in JSON format and the reply from the web server returned crucial information such as the roleid.

![req and res](img/user_role_mod/req%20and%20res.png)

Next, we decided to change the roleid and set it to be 2, as stated in the challenge description.

![roleid 2](img/user_role_mod/updated%20roleid.png)

Finally, we were able to delete the user `Carlos` on the admin panel, allowing us to complete this challenge.

![completed](img/user_role_mod/solved.png)