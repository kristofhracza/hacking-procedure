# MSSQL
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
SELECT * FROM information_schema.schemata;

-- Tables from DB
SELECT * FROM information_schema.tables;

-- Columns from table
SELECT * FROM information_schema.columns;
```

### References
- [https://www.mssqltips.com/sqlservertutorial/179/sql-server-information-schema-views-tutorial/](https://www.mssqltips.com/sqlservertutorial/179/sql-server-information-schema-views-tutorial/)

## Queries
### UNION
When an application is vulnerable to SQL injection and the results of the query are returned within the application’s responses, the UNION keyword can be used to retrieve data from other tables within the database. This results in an SQL injection UNION attack.

#### Examples
```sql
SELECT a, b FROM table1 UNION SELECT c, d FROM table2
```

#### Exploit examples encountered in CTFs
```sql
abcd' union select 1, concat(<data>,':',<data>) 3,4,5,6 from <table>;-- -
```