# Manual enumeration

## System enumeration

```text
hostname

# enumerate os version
uname -a
cat /etc/issue
cat /proc/version
cat /etc/*-release

# enumerate number of cores
lscpu

# enumerate running services
ps aux

# enumerate installed packages
dpkg -l
```

## User enumeration

```text
# whoami
whoami
id

# what can I run as an admin
sudo -l

# enumerate users
cat /etc/passwd
cat /etc/passwd | cut -d : -f 1
cat /etc/passwd | grep "sh$"

# enumerate groups
cat /etc/group
```

## Network enumeration

```text
# enumerate network interfaces
ip a
ifconfig

# enumerate routing table
ip route
routel

#enumerate arp table
ip neigh
arp -a

# enumerate open ports and connected clients
netstat -ano
netstat -anp
ss -anp
cat /etc/iptables
```

## Hunting for interesting files

```text
# enumerate by file contents
grep --color=auto -rnw '/' -ie "password" --color=always >/dev/null
grep --color=auto -rnw '/' -ie "pass" --color=always >/dev/null
grep --color=auto -rnw '/' -ie "pwd" --color=always >/dev/null

# enumerate by file name
locate password | more
locate pass | more
locate pwd | more

# search for ssh keys
find / -name id_rsa 2>/dev/null
```

