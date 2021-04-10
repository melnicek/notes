# Android reversing

## extracting apk

### using adb

```
adb devices
adb shell pm list packages
adb shell pm path <PACKAGE>
adb pull <APK_PATH>
```

### using apktool

```
https://ibotpeaches.github.io/Apktool/install/
apktool d <apk>
```

## Converting .dex file to .jar for easier decompilation

```
https://sourceforge.net/projects/dex2jar/
./d2j-dex2jar.sh ../example.apk/classes.dex -o output_file.jar
./d2j-dex2jar.sh ../example.apk -o output_file.jar
```

## Decompiling .jar file to java source code

```
http://java-decompiler.github.io/

File > Save All Sources
```

## smali/backsmali

```
https://bitbucket.org/JesusFreke/smali/downloads/
java -jar baksmali-2.1.1.jar classes.dex
java -jar smali-2.1.1.jar out/
```

