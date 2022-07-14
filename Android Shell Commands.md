## Downloading APKs
adb shell pm list packages
adb shell pm path PACKAGE
adb pull APKPATH DESTINATION

## Misc
adb shell settings put global http_proxy LHOST:LPORT
adb install APK