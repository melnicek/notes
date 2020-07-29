---
description: source -> destination
---

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

