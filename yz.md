
## portscan
```
sudo rustscan -a $IP --ulimit 8192 -- -sC -sV -o nmap
sudo nmap -sC -sV -v -p- -o nmap $IP
```

## web bruteforce
```
ffuf -w /usr/share/wordlists/directory-list-2.3-small.txt:FUZZ -u http://RHOST:RPORT/FUZZ -recursion -recursion-depth 2 -e '.php,.txt,.html' -t 128 -v
ffuf -w /usr/share/wordlists/directory-list-2.3-big.txt:FUZZ -u http://RHOST:RPORT/FUZZ -recursion -recursion-depth 2 -t 128 -v
```
