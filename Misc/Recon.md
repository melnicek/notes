amass enum -ip -src -d DOMAIN -dir OUT
amass viz -d3 -dir INPUT

theharvester -d DOMAIN -b all
theharvester -d DOMAIN -b all -n

rustscan -a IP -- -sVC -oN nmap-tcp
sudo nmap -sU IP -oN nmap-udp

wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/DNS/subdomains-top1million-110000.txt
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/raft-large-directories.txt

ffuf -w ~/wordlists/raft.txt -u http://IP/FUZZ/
ffuf -w ~/wordlists/subdomains.txt -u http://IP/ -H 'Host: FUZZ.DOMAIN'