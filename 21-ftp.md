# 21 - FTP

## Default creds

```
anonymous : anonymous
anonymous : <blank>
ftp : ftp
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

