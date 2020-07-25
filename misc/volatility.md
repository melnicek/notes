# Volatility

### Obtaining a memory capture from machines

```text
FTK Imager - https://accessdata.com/product-download/ftk-imager-version-4-2-0
Redline - https://www.fireeye.com/services/freeware/redline.html
DumpIt.exe
win32dd.exe || win64dd.exe
```

### These tools will typically output .raw, but another file types are...

```text
VMware - .vmem file
Hyper-V - .bin file
Parallels - .mem file
VirtualBox - .sav file

.raw || %SystemDrive%/hiberfil.sys
```

### Commands

```text
volatility -f MEMORY_FILE.raw imageinfo

# list active processes
volatility -f MEMORY_FILE.raw --profile=PROFILE pslist

# list active network connections
volatility -f MEMORY_FILE.raw --profile=PROFILE netscan

# list hidden processes
volatility -f MEMORY_FILE.raw --profile=PROFILE psxview

# list loaded modules
volatility -f MEMORY_FILE.raw --profile=PROFILE ldrmodules

# list unexpected patches
volatility -f MEMORY_FILE.raw --profile=PROFILE apihooks

# list loaded DLLs
volatility -f MEMORY_FILE.raw --profile=PROFILE dlllist

# dump injected code
volatility -f MEMORY_FILE.raw --profile=PROFILE malfind -D <Destination Directory>
```

