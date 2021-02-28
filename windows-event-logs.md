# Windows event logs

There are 3 main ways of accessing these event logs within a Windows system:
1. Event Viewer (GUI-based application / `eventvwr.msc`)
2. Wevtutil.exe (command-line tool)
3. Get-WinEvent (PowerShell cmdlet)

## 1. Event Viewer

Can be run from terminal using `eventvwr.exe`

## 2. wevtutil.exe

```
wevtutil COMMAND [ARGUMENT [ARGUMENT] ...] [/OPTION:VALUE [/OPTION:VALUE] ...]

Commands:

el | enum-logs          List log names.
gl | get-log            Get log configuration information.
sl | set-log            Modify configuration of a log.
ep | enum-publishers    List event publishers.
gp | get-publisher      Get publisher configuration information.
im | install-manifest   Install event publishers and logs from manifest.
um | uninstall-manifest Uninstall event publishers and logs from manifest.
qe | query-events       Query events from a log or log file.
gli | get-log-info      Get log status information.
epl | export-log        Export a log.
al | archive-log        Archive an exported log.
cl | clear-log          Clear a log.
```
