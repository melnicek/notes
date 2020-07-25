# Port Scanning

```text
ports=$(nmap -p- --min-rate=1000 -T4 <target> | grep ^[0-9] | cut -d '/' -f 1 | tr '\n' ',' | sed s/,$//)
nmap -p$ports -sC -sV <target>
```

### Finding live hosts

```text
fping -a -g <network>/<subnet> 2>/dev/null
nmap -sn <network>/<subnet>
masscan ??? TODO
```

## Nmap

### Scan types

`-sT` TCP connect scan \(slow, can be used without root permissions\)

`-sS` SYN scan \(TCP\)

`-sU` UDP scan \(UDP, good job sherlock\)

`-sS` + `-sU` = &lt;3

`-sn` network sweep

### Scan techniques

`-sV` retrieve version of services running on open ports

`-sC` run default scripts on open ports

`-O` os detection

`-A` all of the above

### Port specification

`-p -` ports from 0-65535

`-p -1023` from 0 to 1023

`-p 50000-` from 50000 to 65535

`--top-ports=<n>` scan only top n ports by frequency

`--exclude-ports=<x,y,z>` exclude ports x, y, z

### Scripts

located in `/usr/share/nmap/scripts`

`--script=<script>` use script

`--script-help=<script>` info about certain script

## masscan

`masscan -p80 <ip>/<subnet>` basic usage

`masscan -p80 <ip>/<subnet> --rate=1000 -e <raw interaface> --router-ip <rip>` advanced usage

## iptables

`iptables -I INPUT 1 -s <target> -j ACCEPT`

`iptables -I OUTPUT 1 -d <target> -j ACCEPT`

`iptables -Z` zero packet byte counter

`iptables -nv -L` list packet byte counter

