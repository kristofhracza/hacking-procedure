# MySQL 

## Connection
```sh
mysql -u <hostname> -u root
mysql -u <hostname> -u root@localhost
```

## Enumeration
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

## Privilege Escalation
### Create user and give privileges
```sql
CREATE USER test identified BY "test";
GRANT SELECT,CREATE,DROP,UPDATE,DELETE,INSERT on *.* to mysql identified by "mysql" WITH GRANT OPTION;
```