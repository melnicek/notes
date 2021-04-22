# ffuf

## installation

```
yay -S ffuf-bin
```

## usage

```
fuzzing
ffuf -w <WORDLIST>:FUZZ -u http://<RHOST>:<RPORT>/FUZZ

extension fuzzing
ffuf -w <WORDLIST>:FUZZ -u http://<RHOST>:<RPORT>/indexFUZZ

recursive fuzzing
ffuf -w <WORDLIST>:FUZZ -u http://<RHOST>:<RPORT>/FUZZ -recursion -recursion-depth 2 -e '.php,.php7' -v

subdomain fuzzing
ffuf -w <WORDLIST>:FUZZ -u https://FUZZ.<DOMAIN>/

vhost fuzzing
ffuf -w <WORDLIST>:FUZZ -u http://<RHOST>:<RPORT>/ -H 'Host: FUZZ.<DOMAIN>' -fs <FILTERSIZE>

parameter fuzzing - GET
ffuf -w <WORDLIST>:FUZZ -u http://<RHOST>:<RPORT>/admin/admin.php?FUZZ=key -fs <FILTERSIZE>	

parameter fuzzing - POST
ffuf -w <WORDLIST>:FUZZ -u http://<RHOST>:<RPORT>/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs <FILTERSIZE>
```

Wordlists
```
SecLists/Discovery/Web-Content/directory-list-2.3-small.txt	
SecLists/Discovery/Web-Content/web-extensions.txt	
SecLists/Discovery/DNS/subdomains-top1million-5000.txt	
SecLists/Discovery/Web-Content/burp-parameter-names.txt	
```
