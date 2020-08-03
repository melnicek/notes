# Cracking hashes

### Identifying hashes

List of example hashes by hashcat: [https://hashcat.net/wiki/doku.php?id=example\_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)

Hash identifier by zulln: [https://hashid.zulln.se/](https://hashid.zulln.se/)

Also this nice built in tool in kali:

```text
hashid <hash>
```

### Cracking non-salted hashes online

```text
https://crackstation.net/
https://www.hashes.org/search.php
https://hashes.com/en/decrypt/hash
```

### Cracking password of id\_rsa

```text
/usr/share/john/ssh2john.py id_rsa > id_rsa.hashes
john -w /usr/share/wordlists/rockyou.txt --format=SSH id_rsa.hashes
```

### Cracking passwd/shadow

```text
# unshadow
/usr/sbin/unshadow passwd shadow >> crack.me

# crack with john
john -wordlist=/usr/share/wordlists/rockyou.txt crack.me

# or with hashcat
hashcat -m 500 -a 0 crack.me /usr/share/wordlists/rockyou.txt -O
```

### Cracking windows hashes

```text
use post/windows/gather/credentials/credential_collector
```

### Cracking .zip/.rar files

```text
zip2john test.zip > zip.hashes
rar2john test.rar > rar.hashes

john zip.hashes
john rar.hashes
```

```text
fcrackzip -v -u -D -p wordlist.txt file.zip
```

