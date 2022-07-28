# SSH remote connections

```
ssh-keygen
vim ~/.ssh/authorized_keys
command="echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-x11-forwarding,no-pty PUBLICKEY
sudo systemctl status ssh
sudo systemctl start ssh
ssh -R LPORT:RHOST:RPORT USER@LHOST -i KEYFILE -fN
```

> By editing the SSH config file (usually /etc/ssh/sshd_config on Linux), you can set the GatewayPorts to yes and then it should allow SSH tunnels to listen on the other interfaces.

```
ssh -R LPORT USER@LHOST -i KEYFILE -fN
```
