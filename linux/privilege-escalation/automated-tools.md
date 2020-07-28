# Automated tools

#### [https://github.com/diego-treitos/linux-smart-enumeration \(lse.sh\)](https://github.com/diego-treitos/linux-smart-enumeration)

#### [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite \(linPEAS.sh\)](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)

[https://github.com/rebootuser/LinEnum \(linEnum.sh\)](https://github.com/rebootuser/LinEnum)

[https://github.com/zet-/linux-exploit-suggester \(les.sh\)](https://github.com/mzet-/linux-exploit-suggester)

[https://github.com/AlessandroZ/BeRoot \(BeRoot.py\)](https://github.com/AlessandroZ/BeRoot)

[https://github.com/sleventyeleven/linuxprivchecker \(linuxprivchecker.py\)](https://github.com/sleventyeleven/linuxprivchecker)

[http://pentestmonkey.net/tools/audit/unix-privesc-check \(unix-privesc-check\)](http://pentestmonkey.net/tools/audit/unix-privesc-check)

## All scripts can be downloaded using this bash script

{% code title="yz-privesc-scripts.sh" %}
```bash
#!/bin/bash
wget "https://raw.githubusercontent.com/diego-treitos/linux-smart-enumeration/master/lse.sh" -O lse.sh
chmod 700 lse.sh
wget "https://raw.githubusercontent.com/carlospolop/privilege-escalation-awesome-scripts-suite/master/linPEAS/linpeas.sh" -O linpeas.sh
chmod 700 linpeas.sh
wget "https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh" -O LinEnum.sh
chmod 700 LinEnum.sh
wget "https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh" -O les.sh
chmod 700 les.sh
wget "https://raw.githubusercontent.com/sleventyeleven/linuxprivchecker/master/linuxprivchecker.py" -O linuxprivchecker.py
chmod 700 linuxprivchecker.py
echo "sudo python3 -m http.server 80"
```
{% endcode %}

