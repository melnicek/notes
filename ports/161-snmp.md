# 161 - SNMP

```text
snmpwalk -c <community_string> -v1 -t <target>

# enumerate users
snmpwalk -c <community_string> -v1 <target> 1.3.6.1.4.1.77.1.2.25

# enum processes
snmpwalk -c <community_string> -v1 <target> 1.3.6.1.2.1.25.4.2.1.2

# enum open TCP ports
snmpwalk -c <community_string> -v1 <target> 1.3.6.1.2.1.6.13.1.3

# enum installed software
snmpwalk -c <community_string> -v1 <target> 1.3.6.1.2.1.25.6.3.1.2 
```





