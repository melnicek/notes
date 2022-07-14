Taken from: https://github.com/Cih2001/String-Obfuscator

First we define macro in the header file.
```c
#ifndef OBFUSCATOR_H__
#define OBFUSCATOR_H__
namespace ob {

#define KEY 0x55

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

Then we can use the macro in our code.
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