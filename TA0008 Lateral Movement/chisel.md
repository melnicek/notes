# chisel

[https://github.com/jpillora/chisel](https://github.com/jpillora/chisel)

## Local Port Forward

Proxy host:
```
./chisel server -p LPORT
```

Attacker host:
```
./chisel client RHOST:RPORT LPORT:RHOST:RPORT
```

## Remote Port Forward

Attacker host:
```
./chisel server -p LPORT --reverse &
```

Proxy host:
```
./chisel client RHOST:RHOST R:LPORT:RHOST:RHOST &
```

## Reverse SOCKS Proxy

On attacker host:
```
./chisel server -p LPORT --reverse &
```

On proxy host:
```
./chisel client RHOST:RPORT R:socks &
```

Then use proxychains.

## Forward SOCKS Proxy

On proxy host:
```
./chisel server -p LPORT --socks5
```

On attacker host:
```
./chisel client RHOST:RPORT LPORT:socks
```

Then use proxychains.
