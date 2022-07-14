---
description: >-
  an open, vendor-neutral, industry standard application protocol for accessing
  and maintaining distributed directory information services over an Internet
  Protocol (IP) network
---

# 389 - LDAP

```text
nmap -p 389 --script ldap-search <target>
```

```text
enum4linux [-u <user> -p <pass>] <target>
```

```text
https://github.com/ropnop/windapsearch

windapsearch --dc <target> -m users
windapsearch --dc <target> -m users --full
windapsearch --dc <target> -m users --attrs description
```

