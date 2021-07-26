# Host discovery

## Living of the land

```bash
for i in {1..254}; do (ping -c 1 x.x.x.${i} | grep "bytes from" &); done
```

```bash
for i in {1..65535}; do (echo > /dev/tcp/x.x.x.x/$i) >/dev/null 2>&1 && echo $i is open; done
```

## Host discovery using nmap

```bash
nmap -sn RANGE
```

## Host discovery using fping

```bash
fping -q -a -g RANGE
```

## Host discovery using reverse DNS lookup

```bash
nmap -sS -p53 RANGE
nmap -sU -p53 RANGE
# then use reverse dns lookup on newly discovered servers
```
