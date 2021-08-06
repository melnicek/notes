# Host discovery

## Linux
```bash
for i in {1..254}; do (ping -c 1 x.x.x.${i} | grep "bytes from" &); done; sleep 2; arp -en | grep -v incomplete
for i in {1..65535}; do (echo > /dev/tcp/x.x.x.x/$i) >/dev/null 2>&1 && echo $i is open; done
```
