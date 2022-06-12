# SEETF (Sourceless Guessy Web (Baby Flag))

## Challenge: 

![challenge description](img/sgw_lfi/chall%20desc.png)

## Solution:
This challenge requires us to perform [Local File Inclusion](https://portswigger.net/web-security/file-path-traversal) to obtain the flag.

Heading over to the challenge site, we saw a GET parameter with the value whysoserious.

![default page](img/sgw_lfi/default.png)

Trying out LFI to /etc/password, by modifying the GET parameter to be `../../../../../../../../etc/passwd`, we obtained the flag in the source code.

![lfi](img/sgw_lfi/lfi.png)

![source code](img/sgw_lfi/view%20source.png)



