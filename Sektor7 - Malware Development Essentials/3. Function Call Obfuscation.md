# TLDR:
```c
typedef LPVOID (WINAPI *pVirtualAlloc_t)(LPVOID lpAddress,SIZE_T dwSize,DWORD flAllocationType,DWORD flProtect);
typedef VOID (WINAPI *pRtlMoveMemory_t)(VOID UNALIGNED* Destination, VOID UNALIGNED* Source, SIZE_T Length);
typedef BOOL (WINAPI *pVirtualProtect_t)(LPVOID lpAddress, SIZE_T dwSize, DWORD flNewProtect, PDWORD lpflOldProtect);
typedef HANDLE (WINAPI *pCreateThread_t)(LPSECURITY_ATTRIBUTES lpThreadAttributes, SIZE_T dwStackSize, LPTHREAD_START_ROUTINE lpStartAddress, __drv_aliasesMem LPVOID lpParameter, DWORD dwCreationFlags, LPDWORD lpThreadId);
typedef DWORD (WINAPI *pWaitForSingleObject_t)(HANDLE hHandle,DWORD dwMilliseconds);

pVirtualAlloc_t pVirtualAlloc = (pVirtualAlloc_t)GetProcAddress(GetModuleHandleA("kernel32.dll"), "VirtualAlloc");
pRtlMoveMemory_t pRtlMoveMemory = (pRtlMoveMemory_t)GetProcAddress(GetModuleHandleA("ntdll.dll"), "RtlMoveMemory");
pVirtualProtect_t pVirtualProtect = (pVirtualProtect_t)GetProcAddress(GetModuleHandleA("kernel32.dll"), "VirtualProtect");
pCreateThread_t pCreateThread = (pCreateThread_t)GetProcAddress(GetModuleHandleA("kernel32.dll"), "CreateThread");
pWaitForSingleObject_t pWaitForSingleObject = (pWaitForSingleObject_t)GetProcAddress(GetModuleHandleA("kernel32.dll"), "WaitForSingleObject");
```

# Encrypting Strings

## Create text file with your strings:
```
VirtualAlloc
RtlMoveMemory
VirtualProtect
CreateThread
WaitForSingleObject
```

## Encrypt them with this python script:
```python
import sys, os
from itertools import cycle


XOR_FUNCTION='''void XOR(unsigned char* payload, int payloadSize, unsigned char* xorKey, int xorKeySize) {
    for (int i = 0; i < payloadSize; i++) {
        payload[i] = payload[i] ^ xorKey[i % xorKeySize];
    }
}'''


def hexify(arr):
    result = ""
    for num in arr:
        if len(hex(num)) == 3:
            result += hex(num).replace("0x", "\\x0")
        else:
            result += hex(num).replace("0x", "\\x")
    return result
  
  
def handle_string(string: str):
    var_name = "string" + string
    key = os.urandom(4)
    enc = [x ^ y for (x, y) in zip(bytes(string, encoding='ascii'), cycle(key))]
    print()
    print('unsigned char {}[] = \"{}\";'.format(var_name, hexify(enc)))
    print('int {}Size = {};'.format(var_name, len(string)))
    print('unsigned char {}Key[] = \"{}\";'.format(var_name, hexify(key)))
    print('int {}KeySize = {};'.format(var_name, len(key)))
    print("XOR({0}, {0}Size, {0}Key, {0}KeySize);".format(var_name))

  
def main():
    if len(sys.argv) != 2:
        print("Usage: {} <file>".format(sys.argv[0]))
        sys.exit()
    print(XOR_FUNCTION)
    with open(sys.argv[1], "r") as f:
        for line in f:
            handle_string(line.strip())
  
  
if __name__ == "__main__":
    main()
```

## Profit:
```c
void XOR(unsigned char* payload, int payloadSize, unsigned char* xorKey, int xorKeySize) {
    for (int i = 0; i < payloadSize; i++) {
        payload[i] = payload[i] ^ xorKey[i % xorKeySize];
    }
}

unsigned char stringVirtualAlloc[] = "\x11\xd3\x54\x4d\x32\xdb\x4a\x78\x2b\xd6\x49\x5a";
int stringVirtualAllocSize = 12;
unsigned char stringVirtualAllocKey[] = "\x47\xba\x26\x39";
int stringVirtualAllocKeySize = 4;
XOR(stringVirtualAlloc, stringVirtualAllocSize, stringVirtualAllocKey, stringVirtualAllocKeySize);

unsigned char stringRtlMoveMemory[] = "\x21\x93\x2e\xd5\x1c\x91\x27\xd5\x16\x8a\x2d\xea\x0a";
int stringRtlMoveMemorySize = 13;
unsigned char stringRtlMoveMemoryKey[] = "\x73\xe7\x42\x98";
int stringRtlMoveMemoryKeySize = 4;
XOR(stringRtlMoveMemory, stringRtlMoveMemorySize, stringRtlMoveMemoryKey, stringRtlMoveMemoryKeySize);

unsigned char stringVirtualProtect[] = "\x0c\x48\xad\xd2\x2f\x40\xb3\xf6\x28\x4e\xab\xc3\x39\x55";
int stringVirtualProtectSize = 14;
unsigned char stringVirtualProtectKey[] = "\x5a\x21\xdf\xa6";
int stringVirtualProtectKeySize = 4;
XOR(stringVirtualProtect, stringVirtualProtectSize, stringVirtualProtectKey, stringVirtualProtectKeySize);

unsigned char stringCreateThread[] = "\x9e\x0a\x81\x6d\xa9\x1d\xb0\x64\xaf\x1d\x85\x68";
int stringCreateThreadSize = 12;
unsigned char stringCreateThreadKey[] = "\xdd\x78\xe4\x0c";
int stringCreateThreadKeySize = 4;
XOR(stringCreateThread, stringCreateThreadSize, stringCreateThreadKey, stringCreateThreadKeySize);

unsigned char stringWaitForSingleObject[] = "\xe9\xcd\x22\xeb\xf8\xc3\x39\xcc\xd7\xc2\x2c\xf3\xdb\xe3\x29\xf5\xdb\xcf\x3f";
int stringWaitForSingleObjectSize = 19;
unsigned char stringWaitForSingleObjectKey[] = "\xbe\xac\x4b\x9f";
int stringWaitForSingleObjectKeySize = 4;
XOR(stringWaitForSingleObject, stringWaitForSingleObjectSize, stringWaitForSingleObjectKey, stringWaitForSingleObjectKeySize);  
```

# Is there another way to encrypt strings? Yes there is.

## We define the macro in a header file:
```c
#ifndef OBFUSCATOR_H__
#define OBFUSCATOR_H__
namespace ob {

#define KEY 0x89

template <unsigned int N>
struct obfuscator {

    char m_data[N] = {0};

    constexpr obfuscator(const char* data) {
        for (unsigned int i = 0; i < N; i++) {
            m_data[i] = data[i] ^ KEY;
        }
    }

    void deobfoscate(unsigned char * des) const{
        int i = 0;
        do {
            des[i] = m_data[i] ^ KEY;
            i++;
        } while (des[i-1]);
    }
};

#define STR(str) \
    []() -> char* { \
        constexpr auto size = sizeof(str)/sizeof(str[0]); \
        constexpr auto obfuscated_str = obfuscator<size>(str); \
        static char original_string[size]; \
        obfuscated_str.deobfoscate((unsigned char *)original_string); \
        return original_string; \
    }()

}

#endif
```

## Which we can then use in our code:
```c
#include "obfuscator.hh"
#include "stdio.h"

using namespace ob;

auto gstr = STR("Global HELLO\n");
int main() {
    printf("%s", gstr);
    printf("%s", STR("Stack HELLO\n"));
    printf("%s", "raw HELLO\n");
    return 0;
}
```

## Code taken from: https://github.com/Cih2001/String-Obfuscator

