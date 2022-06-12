# SEETF (Official Cash Banking Company)

## Challenge: 

![challenge description](img/batnet1/chall%20desc.png)

## Solution:
The challenge requires us to find 4 social media accounts with the username "1mn0tb4tm4m" 

Firstly, we used [sherlock](https://github.com/sherlock-project/sherlock) to find the various social media accounts. From the output, we found 1mn0tb4tm4m's Github and Instagram accounts.

![sherlock output](img/batnet1/sherlock%20output.png)

Heading over to the Github account, we found part 3 flag. 

![part3](img/batnet1/part3.png)

Going to the Instagram account, we found the part 1 flag.

![part1](img/batnet1/part1.png)

Next, we decided to search the profile on Tiktok. Sure enough, we found the part 2 flag.

![part2](img/batnet1/part2.png)

Finally, we decided to head over to the SEETF offical Discord server and found a user named "1mn0tb4tm4m". The user's bio is a base64 encoded string which could be decoded to the part 4 flag.

![part4](img/batnet1/part4.png)

```
part 4/4
_ab0ut_3very0ne}
```

Combing the 4 flag fragments, we obtained the complete flag `SEE{br0th3r_3y3_help_m3_f1nd_0ut_ab0ut_3very0ne}`