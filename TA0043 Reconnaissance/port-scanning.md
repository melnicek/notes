# Port scanning

## Rustscan

> Nmap? More like slowmap.🐢

Rustscan is way faster than raw nmap.

```bash
rustscan -a RHOST --ulimit 8000 -- -sV -oN nmap
```
