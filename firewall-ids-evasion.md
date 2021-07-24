# Firewall/IDS evasion

## Fragmentation

Fragmenting of packets can be turned on using `-f` option in nmap.

Fragmentations doesn't work with every type of scan, for example `-sT` or `-sV`.

Instead of using `-f`, we can use `--mtu` for custom offset size (must be a multiple of 8). 

## Decoys

Evasion using decoys works by sending packets from spoofed IP's (it's best if spoofed IP's are from real machines).

Decoys can be turned on using `-D` option.

```bash
nmap -sS -D DECOY1,DECOY2,DECOY3,ME RHOST
```

Without specifying `ME`, nmap will send scans with your IP in a random position.

You cannot use `-sT` and `-sV` scans with decoy mode enabled.

## Timing

Only slows down scanning, doesn't alter packets in any way.

Use `-T` option to specify timing

|Option|Delay|
|---|---|
|-T0|5 min|
|-T1|15 sec|
|-T2|0,4 sec|
|-T3|default|
|-T4|10 millisec|
|-T5|5 millisec|

## Source ports

Changing source port to 53, 80 or 443 can also help

Use `--source-port` or `-g` option

```bash
nmap -sS --source-port 53 RHOST
```
