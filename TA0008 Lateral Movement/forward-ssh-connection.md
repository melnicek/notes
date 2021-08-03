# Forward SSH connection

## Port forwarding

```
ssh -L LPORT:RHOST:RHOST USER@RHOST -fN
```

## Proxies

```
ssh -D LPORT USER@RHOST -fN
cp /etc/proxychains.conf .
vim proxychains.conf
proxychains COMMAND
```
