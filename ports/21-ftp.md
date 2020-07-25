# 21 - FTP

## Default creds

_anonymous : anonymous  
anonymous : &lt;blank&gt;  
ftp : ftp_

```text
ftp <target>
> <username>
> <password>
> ls -a #don't forget to list hidden files too
> binary
> ascii
> bye
```

## Download all files from FTP

```bash
wget -m ftp://anonymous:anonymous@<target>
wget -m --no-passive ftp://anonymous:anonymous@<target>
```

