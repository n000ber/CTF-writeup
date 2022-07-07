# SPIROS (Cheatsheet)

## Cheatsheet: 

### Mapping

| Application | Description |
| --- | ---|
| whois | domain information|
| dig | DNS lookup |
| Wappalyzer | Web app fingerprinting|
| lbd | check load balancer |
| wafw00f | check web app firewall |
| sslscan | TLS version |

### Enumeration

Do note that ICT wifi will not allow aggressive scanning...

| Application | Command|
|---|---|
|dirsearch| `dirsearch -u 10.10.255.124 -X -x 400,500 -r -f -t 100 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt` |
| nmap | `nmap -sV -T2 $IP`



### Generating wordlist with all 3-digit numbers
```
for i in `seq 0 999`; do printf "%03d\n" $i; done
```

