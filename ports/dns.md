# 53 - DNS

```text
nslookup
> server <target>
> <target>
> 127.0.0.1
> 127.0.0.2
> 127.0.0.5
```

## Types of DNS records

```text
NS -> nameserver
A ->  host record
MX -> mail exchange
PTR -> pointer record for reverse lookups
CNAME -> to create aliases for host records
TXT -> arbitrary data
```

## host

```text
# reading DNS records
host -t <type> <domain> [dns server] 
for t in a aaaa caa cname mx ns srv txt ptr; do host -t $t <domain>; done | grep -v "has no"

# domain transfer (try all DNS servers)
host -l <domain> <dns server> 
for s in $(host -t ns <domain> | cut -d " " -f 4); do host -l <domain> $s; done

for h in $(cat hosts.txt); do host $h.<domain>; done | grep -v "not found"
```

## dnsrecon

```text
dnsrecon -d <domain> -t axfr
dnsrecon -d <domain> -D <subdomain list> -t brt
```

## dnsenum

```text
dnsenum <domain>
```

