
# Location of Registry Hives
%WinDir%/System32/Config
- SAM
- SECURITY
- SYSTEM
- SOFTWARE
- DEFAULT

%WinDir%/appcompat/Programs/AMCACHE.hve

%WinDir%/System32/Config/RegBack
- SAM
- SECURITY
- SYSTEM
- SOFTWARE
- DEFAULT

%UserProfile%/NTUSER.DAT

%UserProfile%/AppData/Local/Microsoft/Windows/USRCLASS.DAT

# Viewing Registry Hives
- Registry Explorer.exe ([link](https://ericzimmerman.github.io/#!index.md))
- cafae.exe ([link](https://tzworks.com/prototype_page.php?proto_id=19))
- RegEdit.exe

| Location | Info |
| --- | --- |
| SOFTWARE/Microsoft/Windows NT/CurrentVersion | OS Version |
| SYSTEM/Select | CurrentControlSet |
| SYSTEM/CurrentControlSet/Control/ComputerName/ComputerName | Computer Name |
| SYSTEM/CurrentControlSet/Control/TimeZoneInformation | Time Zone |
| SYSTEM/CurrentControlSet/Services/Tcpip/Parameters/Interfaces | Network Interfaces |
| SYSTEM/CurrentCOntrolSet/Services/lanmanserver/Share | Shares |

