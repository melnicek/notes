---
description: >-
  enables an administrator to remotely manage many Windows Servers from a
  central location
---

# 5985 - PS remoting

```text
PS> Enter-PSSession <target>
```

```text
sudo gem install evil-winrm
evil-winrm -u <user> -p <pass> -i <target>
> upload <file>
```

```text
python3 wmiexec.py -hashes <hash:hash> <user>@<target>
```

```text
crackmapexec winrm <target> -u <user> -p <pass>
crackmapexec winrm <target> -u <user> -p <pass> -X "<command>"
```

