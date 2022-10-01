# Security Analytics Cheatsheet 

## Wireshark

|Command| Explanation|
|---|---|
|`tcp.dstport !=80 && tcp.dstport != 22`| Packets that doesn't contain HTTP and SSH ports|
|`tcp.flags.syn == 1 && tcp.flags.ack==0`|Query for first packet of tcp 3 way handshake (statistics --> conversation)|
|`(tcp.port == 80 \|\| http) && tcp.len > 0 && ip.dst == 192.168.0.10`| HTTP protocol and packet must contain data|
|`http.request.method == GET && ip.dst == 192.168.0.10`| Finds the GET request to the web server|
|`tftp.source_file`| See if any file is transferred over tftp protocol|

## GREP/AWK

|Command| Explanation|
|---|---|
|`grep UDP netflow-ddos.txt \| awk '{print $5}\| awk -F:'{print $1}' \| sort -u`| Obtain unique source IP address from netflow logs (first field is field 1, NOT field 0)|
|`grep UDP netflow-ddos.txt \| awk '{print $5}\| awk -F:'{print $1}' \| sort -u \| wc -l`| Number of unique source IP addr from netflow logs|
|`grep -v UDP netflow-ddos.txt`| Filter out UDP packets|
|`grep -v UDP netflow-ddos.txt \| awk '{print $7} \| grep :80 \| sort \ uniq -c \| sort -rn`| Filters non-udp packets, find the unique destination IP address that has port 80 and sort the results in descending order | 
|`grep 10.16.54.6 netflow-ddos.txt\| grep -v UDP\| awk '{print $5}' \| awk -F: '{print $1} \| sort \| uniq -c \| sort -rn `| Finds the source IP address that connects to the web server in descending order.|
|`egrep "85.128.40.3\|66.249.72.45" netflow-ddos.txt`| Grep either of the 2 IPs from the netflow logs|

## Finding Geolocation of a list of IPs

| Command | Explanation |
| --- | --- |
| `netcat whois.cynru.com 43 < list01` | Query whois to obtain the country for each IP in list01 |

| Command | Description |
| --- | --- |
| `git status` | List all *new or modified* files |
| `git diff` | Show file differences that **haven't been** staged |