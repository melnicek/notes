```c
#pragma comment(linker, "/ENTRY:entry")

int entry() {
	return 8990;
}
```

Properties > Configuration Properties > C/C++ > Optimization
- Whole Program Optimization = No

Properties > Configuration Properties > C/C++ > Code Generetion
- Enable C++ Exceptions = No
- Security Check = Disable Security Check (/GS-)

Properties > Configuration Properties > Linker > Input
- Ignore All Default Libraries = Yes (/NODEFAULTLIB)

https://www.youtube.com/watch?v=TfG9lBYCOq8