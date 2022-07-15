# SPIROS (Quiz 2)

## Challenge: 

![challenge description](img/quiz2/chall%20desc.jpg)

This quiz consists of 9 parts, to be done within 75 minutes (8 min 20 sec for 1 part). 

|Part|Tested on|Difficulty (upon 5 *)|
|---|---|--|
|[1](###-part-1-solution)|Finding hidden directory|*|
|[2](###-part-2-solution)|Brute-forcing password|*|
|[3](###-part-3-solution)|Cookie attack (Timestamp)|**|
|[4](###-part-4-solution)|Cookie attack (Structured tokens)|**|
|[5](###-part-5-solution)|Referer header|*|
|[6](###-part-6-solution)|Insecure file naming|**|
|[7](###-part-7-solution)|Insecure admin validation|****|
|[8](###-part-8-solution)|Changing request method|****|
|[9](###-part-9-solution)|Bypassing 2FA|****|

## Solution:

### Part 1 Solution
Part 1 requires us to use a brute-force script to find the directory that contained login.php.

Using the following command, I was able to generate a 3-digit wordlist called `000-999.txt`.

```sh
for i in `seq 0 999`; do printf "%03d\n" $i; done > 000-999.txt
```

Afterwards, I wrote the following bash script to brute-force each directory using cURL.

```sh
#!/bin/bash

for p in $(cat /home/kali/Documents/quiz2/000-999.txt)
do
    echo $p
    curl https://websec.spiros.ml/$p/login.php
done
```
Not long after, I found that the hidden directory was `/041`.

![brute_entry output](img/quiz2/found%20the%20hidden%20dir.jpg)

### Part 2 Solution
Part 2 requires us to brute-force the entry page. Heading to the `/login.php`, I was required to sign in.

![sign in](img/quiz2/login%20php%20page.jpg)

Firstly, I used Burpsuite's Proxy to obtain the authentication mechanism being used on the entry page.

![diges authentication](img/quiz2/burp%20login%20php%20page.jpg)

Upon knowing that Basic authentication was being used, I wrote the following script to brute-force for the password.

```sh
#!/bin/bash

for p in $(cat /usr/share/wordlists/rockyou.txt)
do
	echo $p
	curl -u ict2206:$p https://websec.spiros.ml/041/login.php 
done
```

After running the script, the password `secret` was obtained.

![obtained password](img/quiz2/secret%20is%20the%20password.jpg)

With the newly found credentials, I was able to log in to the challenge page.

![challenge page](img/quiz2/able%20to%20login%20to%20welcome%20html.jpg)

### Part 3 Solution
Part 3 requires us to modify the Unix Epoch timestamp that was provided and to set it as the cookie.
> Common pitfall: Modifying the current Epoch timestamp instead of the provided Epoch timestamp.

Visiting the page, I did not have the permissions to view it.

![no perm to view](img/quiz2/unable%20to%20access%20session1.jpg)

Afterwards, I wrote a bash script to increment the provided Unix timestamp up till 120, since the cookie to be found must be within 2 minutes of the provided Unix timestamp. 

```sh
for i in `seq 1 120`; do printf "%d\n" $i; done > 1-120.txt
```

```sh
#!/bin/bash

for p in $(cat /home/kali/Documents/spiros/quiz2/1-120.txt):
do
    y=$(($p+1657010501))
    echo $y
    curl --cookie SessionID=$y https://websec.spiros.ml/041/session1.php
done
```

> Do note that the use of $(()) is an arithmetic operator in bash.

Upon running the script, I obtained the flag for part 3, when the cookie was `SessionID=1657010556`.

![part 3 flag](img/quiz2/got%20session%201%20flag.jpg)

### Part 4 Solution
Part 4 requires us to decode the given SessionID and modify it to be able to obtain the flag.

Using Cyberchef, I was able to decode the SessionID to obtain the string `user=alice; date=30/06/2022; seq=3` and `user=bob; date=30/06/2022; seq=7`.

![session id decoded](img/quiz2/session%20id%20decoded%20base64.jpg)

![second sess id decoded](img/quiz2/second%20decoded%20b64.jpg)

Knowing that the cookie to be found has the username `admin` and the same date of `30/06/2022`, only the sequence number is left for us to brute-force.

Manually trying the sequence number, I eventually found the flag when using `seq=9`.

![final payload part 3](img/quiz2/correct%20payload%20for%20sess%202.jpg)

![found part 3 flag](img/quiz2/sess2%20flag.jpg)

### Part 5 Solution
Part 5 requires us to use the `Referer` header to be able to view the flag.

Upon visiting part 5 webpage, we were greeted with the following.

![part 5 webpage](img/quiz2/sess3%20webpage.jpg)

After intercepting the request and adding the `Referer=https://websec.spiros.ml/admin/index.php` header, I was able to obtain the flag.

![adding referer](img/quiz2/sess3%20flag%20found.jpg)

### Part 6 Solution
Part 6 requires us to decode the file naming convention and modify it to obtain the flag.

Upon visiting the site for part 6, the long file name stood out to me.

![part 6 site](img/quiz2/sess4%20webpage.jpg)

Using Cyberchef, it was decoded to `ict2206:doc=5`.

![part 6 flag](img/quiz2/sess4%20cyberchef.jpg)

Given that the username is `ict2210`, we needed to brute-force the `doc` parameter.

Manually trying different values for the `doc` parameter, I found the flag when using `ict2210:doc=3`. 

![part 6 final payload](img/quiz2/sess4%20final%20payload.jpg)

![part 6 flag](img/quiz2/sess4%20flag.jpg)

### Part 7 Solution
Part 7 requires us to have an intuition on the possible admin validation methods.

Visiting the site, I was greeted with the following.

![part 7 site](img/quiz2/q7%20page.jpg)

Seeing that it is an admin page that was adding user, insecure admin validation methods like `?admin=True`, `?admin=1` and `?admin=yes` could be implemented.

Adding the GET parameter `?admin=yes`, I obtained the flag.

![part 7 flag](img/quiz2/q7%20flag.jpg)

### Part 8 Solution
Part 8 is similar to part 7 but requires the changing of the request method.

Intercepting my request to the site, I saw the following.

![part 8 response](img/quiz2/q8%20request%20page.jpg)

From there, I inferred that the `pass` parameter was crucial in solving this challenge.

Firstly, I added the `?pass=true` parameter, which still showed an error message.

Next, right-clicking and selecting `Change request method`, will change it from a `GET` request to a `POST` request.

![change request method](img/quiz2/change%20req%20meth.jpg)

Submitting the `POST` data as `pass=true`, I obtained the flag.

![q8 flag](img/quiz2/q8%20pass=true%20to%20get%20flag.jpg)

#### Part 9 Solution
Part 9 requires us to analyze the 2FA functionality and bypass it by changing the `verified` and `PHPSESSID` cookie values.

Upon visiting the site, I was greeted with a login page.

![login page](img/quiz2/final%20qn%20login%20page.jpg)

Logging in with the given `ict2206` credentials, a 2FA appeared.

![2fa](img/quiz2/final%20qn%202fa.jpg)

Upon entering the given 2FA code of `123456` for the user `ict2206`, I was redirected to the `/home.php` page. From the `Cookie` field, it is evident that there were 2 cookies, namely the `verified` cookie and the `PHPSESSID` cookie.

![home php page](img/quiz2/final%20qn%20after%20loggin%20in%20to%202fa.jpg)

Using Cyberchef's Analyze hash functionality, the `verified` cookie value could be a MD5 hash.

![md5 hash verified](img/quiz2/final%20qn%20md5%20from%20verified.jpg)

Trying to decrypt MD5 hash, it returned `ict2206`. 

![decoded md5](img/quiz2/final%20qn%20decoded%20md5.jpg)

> It is imperative to check if the MD5 hash is derived from the known values (eg: username).

Knowing that the value of `verified` is just the MD5 hash of the username, I am able to get the `verified` value using the following.

![obtaining md5 of 2203](img/quiz2/final%20qn%20md5%202203.jpg)

Now, all that's left is the `PHPSESSID` of `ict2203` user.

Opening a new incognito tab and logging in as the `ict2203` user, I was able to obtain the `PHPSESSID` for the user.

![obtaining phpsessid](img/quiz2/final%20qn%20sess%20id%20of%20the%202203%20user.jpg)

Modifying the `verified` and `PHPSESSID` cookies values for `/home.php`, I was able to obtain the final flag. :')

![got final flag](img/quiz2/final%20qn%20got%20flag.jpg)


