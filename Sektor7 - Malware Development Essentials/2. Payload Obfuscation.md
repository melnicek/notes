# Base64
[recipe](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')To_Base64('A-Za-z0-9%2B/%3D'))

Definition:
```c
#include <Wincrypt.h>
#pragma comment(lib, "crypt32.lib")

int DecodeBase64(unsigned char* src, unsigned char* dst, unsigned int srcSize) {
	DWORD dstSize = srcSize;
	CryptStringToBinaryA((LPCSTR)src, srcSize, CRYPT_STRING_BASE64, (BYTE*)dst, &dstSize, NULL, NULL);
	return 0;
}
```

Usage:
```c
unsigned char payload[] = "....SNIP....";
unsigned int payloadSize = sizeof(payload);

DecodeBase64(payload, (unsigned char *)allocatedMemory, payloadSize);
```

TODO: in-place base64

# XOR
[recipe](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')XOR(%7B'option':'Hex','string':'8990'%7D,'Standard',false)To_Hex('0x%20with%20comma',0))

Definition:
```c
int XOR(unsigned char* payload, int payloadSize, unsigned char* xorKey, int xorKeySize) {
	for (unsigned int i = 0; i < payloadSize; i++) {
		payload[i] = payload[i] ^ xorKey[i % xorKeySize];
	}
	return 0;
}
```

Usage:
```c
unsigned char payload[] = {
	....SNIP....
};
unsigned char xorKey[] = {
	....SNIP....
};
int payloadSize = sizeof(payload);
int xorKeySize = sizeof(xorKey);

XOR(payload, payloadSize, xorKey, xorKeySize);
```

# AES
[generate random aes key](https://gchq.github.io/CyberChef/#recipe=Pseudo-Random_Number_Generator(32,'Raw')To_Hex('0x%20with%20comma',0))
[hash generated aes key](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')SHA2('256',64,160))
[encrypt payload with hashed key](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')AES_Encrypt(%7B'option':'Hex','string':''%7D,%7B'option':'Hex','string':'0000000000000000000000000000000000000000000000000000000000000000'%7D,'CBC','Raw','Raw',%7B'option':'Hex','string':''%7D)To_Hex('0x%20with%20comma',0))

Definition:
```c
#include <psapi.h>
#include <wincrypt.h>
#pragma comment (lib, "advapi32")
#pragma comment (lib, "crypt32.lib")

int AESDecrypt(unsigned char * payload, DWORD payloadSize, unsigned char* aesKey, size_t aesKeySize) {
    HCRYPTPROV hProv;
    HCRYPTHASH hHash;
    HCRYPTKEY hKey;

    if (!CryptAcquireContextW(&hProv, NULL, NULL, PROV_RSA_AES, CRYPT_VERIFYCONTEXT)) return -1;
    if (!CryptCreateHash(hProv, CALG_SHA_256, 0, 0, &hHash)) return -1;
    if (!CryptHashData(hHash, (BYTE*)aesKey, (DWORD)aesKeySize, 0)) return -1;
    if (!CryptDeriveKey(hProv, CALG_AES_256, hHash, 0, &hKey)) return -1;
    if (!CryptDecrypt(hKey, (HCRYPTHASH)NULL, 0, 0, (BYTE *)payload, &payloadSize)) return -1;

    CryptReleaseContext(hProv, 0);
    CryptDestroyHash(hHash);
    CryptDestroyKey(hKey);

    return 0;
}
```

Usage:
```c
unsigned char payload[] = { 
	....SNIP....
};
unsigned char aesKey[] = {
	....SNIP....
};
unsigned int payloadSize = sizeof(payload);
unsigned int aesKeySize = sizeof(aesKey);

AESDecrypt(payload, payloadSize, aesKey, aesKeySize);
```