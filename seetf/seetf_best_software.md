# SEETF (Best Software)

## Challenge: 

![challenge description](img/best_software/chall%20desc.png)

## Solution:
The challenge requires us to decompile the source code and to generate the correct license key.

After downloading the challenge, we used the `file` command on the program. We found out that the program is a .Net assembly file.

![exe file](img/best_software/file%20cmd.png)

Therefore, we decided to use dnSpy to decompile the file. After decompiling, we found a function called CheckLicenseKey. 

![check license func](img/best_software/check%20license%20func.png)

From line 42, it is evident that the input name is concatenated with the string "1_l0v3_CSh4rp" followed by the input email address. The result is then hashed with SHA256. 

Hence, we tried to hash the "seetf1_l0v3_CSh4rpseetf@seetf.sg", which gave us an output of "28f313a48c1282df95e07bcef466d19517587bcab4f7a78532fa54ac6708444e". 

![online sha256](img/best_software/online%20sha256.png)

However, the program considered the license key as invalid. 

![invalid license key](img/best_software/failed%20flag.png)

> In hindsight, we should have tried the capitalized version of the same string.

Moving on, we tried to set a breakpoint just after the SHA256 was calculated, to allow us to obtain the SHA256 result.

![sha256 result](img/best_software/set%20breakpoint.png)

With the debugger in place, we ran the program and input the name as "seetf" and email as "seetf@seetf.sg". As for the license key, we just input a random string.

![breakpoint pgm](img/best_software/stopped%20process.png)

Heading back to the dnSpy, we can view the result.

![result](img/best_software/obtained%20the%20result.png)

Finally, we obtained the license key to be "28F313A48C1282DF95E07BCEF466D19517587BCAB4F7A78532FA54AC6708444E", which was the capitalized version of the previous SHA256 that we have computed. After we input the correct license key, we got our flag! :)

![flag](img/best_software/flag%20obtained.png)
