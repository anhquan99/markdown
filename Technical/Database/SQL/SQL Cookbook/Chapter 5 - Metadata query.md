## List all table
```sql
SELECT * FROM INFORMATION_SCHEMA.TABLES
```
## Table information
- Problem: List the colmns in a table, along with their data types and their position in the table.
- Solution:
```sql
SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'EMP'
```
## Find indexed column in table
```sql
SELECT A.NAME AS TABLE_NAME, B.NAME AS INDEX_NAME, D.NAME AS COLUMN_NAME, C.INDEX_COLUMN_ID AS INDEX_COLUMN
FROM SYS.TABLES A, SYS.indexes B, SYS.INDEX_COLUMNS C, SYS.COLUMNS D 
WHERE A.OBJECT_ID = B.OBJECT_ID
    AND B.OBJECT_ID = C.OBJECT_ID
    AND C.OBJECT_ID = D.OBJECT_ID
    AND C.COLUMN_ID = D.COLUMN_ID
    AND A.NAME = 'EMP'
```
- <mark style="background: #ADCCFFA6;">Dicussion:</mark> it's important to know wat columns are/aren't indexed. Indexes can provide good performance for queries against columns that are frequently used in filter and that are fairly selective. Indexes are also useful when joining between tables, this can increase performace.
## List constraints on a table
```SQL
SELECT A.TABLE_NAME, A.CONSTRAINT_NAME, B.COLUMN_NAME, A.CONSTRAINT_TYPE 
FROM INFORMATION_SCHEMA.TABLE_CONSTRAINTS A, INFORMATION_SCHEMA.KEY_COLUMN_USAGE B
WHERE A.TABLE_NAME = 'EMP'
    AND A.TABLE_NAME = B.TABLE_NAME
    AND A.TABLE_SCHEMA = B.TABLE_SCHEMA
    AND A.CONSTRAINT_NAME = B.CONSTRAINT_NAME
```
## Dynamic query
- Use string to build SQL statements and pass value you need to query.