# Packet analyzers

### Wireshark display filters

```text
ip.host == 10.10.10.10
ip.src == 10.10.10.10
ip.dst == 10.10.10.10
(http or icmp) and (snd or ssh)
tcp.port == 80
udp.port == 161
tcp.flags.syn == 1
tcp contains password
```

### Tcpdump

```text
tcpdump -i <interface> [-v / -q ] host <host> [-F <filter_file>]
```

