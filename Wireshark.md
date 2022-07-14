https://www.youtube.com/c/ChrisGreer/videos

## Filters
ip.addr / ip.src / ip.dst == IP
tcp.port == PORT

http or dns
http.request
http.response.code == CODE

!(arp or dns or icmp)
tcp.analysis.flags
tcp.flag.syn / tcp.flag.reset == 1

