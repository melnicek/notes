# Shell Kung Fu

### Mounting shared folder

```text
/usr/bin/vmhgfs-fuse .host:/ /mnt -o subtype=vmhgfs-fuse,allow_other
```

### find

`find / -type [d/f] [-name/-iname]`

`-type` -  `d` for directory or `f`for file 

`-name` - searches for name, case sensitive

`-iname` - searches for name, case insensitive

`-user` - files owned by this user

`-size` -  files with size \(ex. -30c is less than 30 bytes, +50k is more than 50 kb\)

`-perm` -  either in octal form \(`644`\) or in symbolic form \(`u=r`\)

`min/time` - minutes /days

`a/m/c` - accessed/modified/changed

`-amin +30` / `-mtime -7`

### working with text files

`awk -F\[separator] ‘{print $[row]}’`

`cut -d [delimeter] -f [field]`

 `sed -e s/foo/bar/g /tmp/file` Replace foo with bar

 `sort -u` sort and make unique

`uniq -c` shows unique and count them 

`${IFS}` can be used as a space 

`comm -12 [file1] [file2]` comparing 2 files 

`diff -c file1 file2` diffing 

`jobs; fg; bg`

```text
find D* -ls
```

