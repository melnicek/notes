---
description: source -> destination
---

# Transfering files

### Netcat

```text
# source
nc -nv <ip> <port> < <file>

# destination
nc -lvnp <port> > <file>
```

### Socat

```text
# source
socat TCP4-LISTEN:<port>,fork file:<src_path>

# destination
socat TCP4:<ip>:<port> file:<dst_path>,create
```

