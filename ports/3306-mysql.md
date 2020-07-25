# 3306 - MySQL

### Dumping database

```text
mysqlcheck --all-databases -u <username> -p

# get database name

mysqldump <database_name> -u <user> -p
```

### Connecting to database

```text
mysql -u root -p
mysql -h <target> -u root -p
```

```text
show databases;
use <database>;
show tables;
select * from <table>;
describe <table>;
```

```text
https://gist.github.com/bradtraversy/c831baaad44343cc945e76c2e30927b3
https://gist.github.com/hofmannsven/9164408
```

