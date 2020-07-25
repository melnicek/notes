---
description: post/multi/recon/local_exploit_suggester
---

# Windows

### Situational awareness

```text
whoami /all
net localgroup administrators
net users
net users <user>
hostname
systeminfo
tasklist /svc
ipconfig /all
route print
netstat -ano
netsh advfirewall show currentprofile
schtasks /query /fo list /v
wmic product get name,version,vendor
wmic qfe get caption,description,installedon

# page 528 for more

type C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt 
```

### Enumeration tools

```text
https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite
https://github.com/absolomb/WindowsEnum
https://github.com/411Hall/JAWS
https://github.com/GhostPack/Seatbelt
```

### GTFOBins for Windows

```text
https://lolbas-project.github.io/
```

### If everything else fails

```text
https://book.hacktricks.xyz/windows/checklist-windows-privilege-escalation

OR

https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md#tools
```

### Loot

```text
C:/windows/system32/config/SAM
C:/windows/system32/config/SYSTEM
impacket-secretsdump -sam SAM -system SYSTEM local
```

```text
GetChangesAll + GetChanges = ADSync attack (Invoke-Mimikatz || secretsdump.py)
```

### 

