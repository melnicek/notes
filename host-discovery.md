# Host discovery

## Host discovery using nmap

```bash
nmap -sn RANGE
```

## Host discovery using fping

```bash
fping -a -g RANGE
```

## Host discovery using reverse DNS lookup

```bash
nmap -sS -p53 RANGE
nmap -sU -p53 RANGE
# then use reverse dns lookup on newly discovered servers
```
