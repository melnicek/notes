# 53 - DNS

## Types of DNS records

```
NS -> nameserver
A -> IPv4 host record
AAAA -> IPv6 host record
MX -> mail exchange
PTR -> pointer record for reverse lookups
CNAME -> to create aliases for host records
TXT -> arbitrary data
```

## Using dig

```
dig DOMAIN
dig +nocmd DOMAIN ns +noall +answer
dig +nocmd DOMAIN axfr +noall +answer @NAMESERVER
```

## Using nslookup

```
nslookup DOMAIN
nslookup -query=ns DOMAIN
nslookup -query=mx DOMAIN
nslookup -query=any DOMAIN
```

or

```
nslookup
> server RHOST
> set q=all
> DOMAIN
> exit
```

## host

```
# reading DNS records
host -t TYPE DOMAIN
for t in a aaaa caa cname mx ns srv txt ptr; do host -t $t DOMAIN; done | grep -v "has no"

# domain transfer (try all DNS servers)
host -l DOMAIN DNSSERVER
for s in $(host -t ns <domain> | cut -d " " -f 4); do host -l <domain> $s; done

for h in $(cat hosts.txt); do host $h.<domain>; done | grep -v "not found"
```

## fierce

```
fierce -dns DOMAIN -dnsserver DNSSERVER
```

## dnsrecon

```
dnsrecon -d <domain> -t axfr
dnsrecon -d <domain> -D <subdomain list> -t brt
```

## dnsenum

```
dnsenum <domain>
```

