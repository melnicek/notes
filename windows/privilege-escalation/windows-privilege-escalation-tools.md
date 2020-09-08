# Privilege Escalation Tools

## [winPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS)

Compiled releases can be found [here](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS/winPEASexe/winPEAS/bin/Obfuscated%20Releases)

```
wget https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/blob/master/winPEAS/winPEASexe/winPEAS/bin/Obfuscated%20Releases/winPEASany.exe?raw=true
```

Before running, we need to add a registry key and then reopen the command prompt:

```
reg add HKCU\Console /v VirtualTerminalLevel /t REG_DWORD /d 1
```

Run all checks while avoiding time-consuming searches:

```
.\winPEASany.exe quiet cmd fast
```

Run specific check categories:

```
.\winPEASany.exe quiet cmd systeminfo
```


## [PowerUp](https://raw.githubusercontent.com/PowerShellEmpire/PowerTools/master/PowerUp/PowerUp.ps1) & [SharpUp](https://github.com/GhostPack/SharpUp)

PowerUp & SharpUp are very similar tools that hunt for specific privilege escalation misconfigurations.

Pre-Compiled SharpUp: https://github.com/r3motecontrol/GhostpackCompiledBinaries/blob/master/SharpUp.exe

PowerUp
To run PowerUp, start a PowerShell session and use dot
sourcing to load the script:

```
. .\PowerUp.ps1
```

Run the Invoke-AllChecks function to start checking for common privilege escalation misconfigurations.

```
Invoke-AllChecks
```

To run SharpUp, start a command prompt and run the executable:

```
.\SharpUp.exe
```

SharpUp should immediately start checking for the same misconfigurations as PowerUp.

## Seatbelt

Seatbelt is an enumeration tool. It contains a number of enumeration checks.

It does not actively hunt for privilege escalation misconfigurations, but provides related information for further investigation.

Code: https://github.com/GhostPack/Seatbelt

Pre-Compiled: https://github.com/r3motecontrol/GhostpackCompiledBinaries/blob/master/Seatbelt.exe

To run all checks and filter out unimportant results:

```
.\Seatbelt.exe all
```

To run specific check(s):

```
.\Seatbelt.exe <check> <check> …
```

## accesschk.exe

AccessChk is an old but still trustworthy tool for checking user access control rights.

You can use it to check whether a user or group has access to files, directories, services, and registry keys.

The downside is more recent versions of the program spawn a GUI “accept EULA” popup window. When using the command line, we have to use an older version which still has an /accepteula command line option.
