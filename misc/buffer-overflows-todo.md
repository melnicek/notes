# Buffer overflows - TODO

1. Fuzzing
2. Inspect crashes in debugger
3. Look for influenced registers
4. Exploit influenced registers
5. Bad chars
6. mona.py

### Finding offset

```text
msf-pattern_create -l <length>
msf-pattern_offset -l <length> -q <eip>

msf-nasm_shell
```

### Immunity debugger

F2 - breakpoint

F7 - step into

F8 - step over

F9 - run

Search for &gt; all referenced text strings \|\| double-click on string

