# Port forwarding / tunneling

### Metasploit

```text
use post/windows/manage/autoroute
set SESSION <session>
set SUBNET <subnet>
run
```

### rinetd

```text
rinetd
cat /etc/rinetd.conf
0.0.0.0 <local_port> <target> <target_port>
sudo service rinetd restart
ss -antp
```

### SSH tunneling

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

