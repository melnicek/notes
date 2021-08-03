# Creating user with RDP privileges

```
net user USER PASS /add
net localgroup Administrators USER /add
net localgroup "Remote Management Users" USER /add
```
