# SEETF (Sniffed Traffic)

## Challenge: 

![challenge description](img/sniff_traffic/challenge_desc.png)

## Solution:
The challenge is to analyze the wireshark data to obtain a password-protected zip file and to subsquently crack the passwords.

After downloading the provided PCAP file, we used wireshark to analyze the packets. Using Wireshark's export objects --> HTTP function, we managed to find a zip file named thingamajig.zip. 

![export function](img/sniff_traffic/wireshark%20export%20objects.png)

![thingamajig.zip](img/sniff_traffic/zip%20file%20found.png)

Next, we saved the zip file and proceeded to unzip it. However, it was password-protected.

![password-protected zip](img/sniff_traffic/password%20protected.png)

Next, we went back to Wireshark for further analysis by following the tcp stream of the zip file. 

![tcp stream](img/sniff_traffic/follow%20tcp%20stream.png)

From the TCP stream, we found that the server was running Python SimpleHTTP. Clicking on the next TCP stream, we found the password for the zip file.

![password for zip](img/sniff_traffic/first%20password.png)

Afterwards, we extracted the first zip file.

![extracted zip](img/sniff_traffic/first%20extraction.png)

After running `strings` on the extracted file, we saw flag.txt as the output.

![strings](img/sniff_traffic/strings%20stuff.png)

This suggests that "stuff" may be a zip file that contains flag.txt. Hence, we tried using the `unzip` command. 

![unzip](img/sniff_traffic/ask%20second%20password.png)

Since we do not know the second password, we wanted to use ``zip2john`` to convert it into a format that john is able to crack. 

![unable to zip2john](img/sniff_traffic/unable%20to%20zip2john.png)

Since we failed running `zip2john`, we decided to use `binwalk` to carve out the zip file.

![carving our zip file](img/sniff_traffic/binwalk%20extract.png)

Next, we tried to use the `zip2john` command again on the file extracted by `binwalk`.

![zip2john success](img/sniff_traffic/zip2john.png)

Moving on, we used `john` and the rockyou.txt worldist to brute force the password.

![brute force](img/sniff_traffic/brute%20force.png)

Using the password "john", we were able to unzip the password-protected file and obtain the flag.
![flag](img/sniff_traffic/flag.png)
