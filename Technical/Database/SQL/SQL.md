# Database
- Is an organized collection of data, stored and retrieved digitally from a remote or local computer system. Databases can be vast and complex, and such databases are developed using fixed design and modeling approaches.
# DBMS (Database Management System)
- System software responsible for the creation, retrieval, update, and management of database. It ensures that data is consistent, organized and easily accessible by serving as an interface between the database and its end users or application software.
# RDBMS (Relational Database Management System)
- Store data in the form of a collection of tables, and relations can be defined between the common fields of these tables.
# SQL (Structured Query Language)
- It is the standard language for relational database management systems.
- Useful in handling organized data comprised of entities and relations between different entities of data
# Tables and fields
- Table is an organized collection of data stored in the form of rows and columns.
- Columns in table are field
# Constraints
- Not null
- Check: Verifies that all values in the field satisfy a condition
- Default: automatically assigns a default value if no value has been specified for the field
- Unique
- Index: Indexes a field, providing faster retrieval of records.
- Primary key
- Foreign key
# Join
- Types of join:
	- <mark style="background: #FFB8EBA6;">Inner join:</mark> theta, natual, [[SQL Cookbook#^abdd24|equi]].
	- <mark style="background: #FFB8EBA6;">Outer join:</mark> left, right, full.
- Is used to combine records from two or more tables in SQL database
  ![[Pasted image 20220315231848.png | 300]]

- <mark style="background: #ADCCFFA6;">Inner join:</mark>
```sql
SELECT * FROM Table_A JOIN Table_B; SELECT * FROM Table_A INNER JOIN Table_B;
```
- <mark style="background: #ADCCFFA6;">Full outer join:</mark>
```sql
SELECT * FROM Table_A JOIN Table_B;
```
- <mark style="background: #ADCCFFA6;">Left join or left outer join:</mark>
```sql
SELECT * FROM Table_A A LEFT JOIN Table_B B ON A.col = B.col;
```
- <mark style="background: #ADCCFFA6;">Right join or right outer join: </mark>
```sql
SELECT * FROM Table_A A RIGHT JOIN Table_B B ON A.col = B.col;
```
- <mark style="background: #ADCCFFA6;">Cross join: Cartesian product</mark>
```sql
SELECT stu.name, sub.subject FROM students AS stu CROSS JOIN subjects AS sub;
```
- <mark style="background: #ADCCFFA6;">Self join:</mark> is a case of regular join where a table is joined to itself based on some relation between its own column(s). Self-join uses the INNER JOIN or LEFT JOIN clause and a table alias is used to assign different names to the table within the query.
```sql
SELECT A.emp_id AS "Emp_ID",A.emp_name AS "Employee",
B.emp_id AS "Sup_ID",B.emp_name AS "Supervisor"
FROM employee A, employee B
WHERE A.emp_sup = B.emp_id;
```
- ![[Pasted image 20220315233030.png | 400]]
# Clustered vs Non-Clustered index
- <mark style="background: #FFB86CA6;">Clustered index:</mark>
	- Modifies the way records are stored in a database based on the indexed column
	- Used for easy and speedy retrieval of data from the database
	- A table can have a single clustered index
- <mark style="background: #FFB86CA6;">Non-Clustered index</mark>:
	- Create entity within the table which references the original table
	- When retrieve slower than clustered index
	- Can have multi non-clustered index in a table
# Common clause used with Select query
- Where
- Order by
- Group by
- Having
# Union
- Operator combines and returns the result-set retrieved by two or more SELECT statements.
```sql
SELECT name FROM Students /* Fetch the union of queries */ UNION SELECT name FROM Contacts; SELECT name FROM Students /* Fetch the union of queries with duplicates*/ UNION ALL SELECT name FROM Contacts;
```
# Minus
- Operator in SQL is used to remove duplicates from the result-set obtained by the second SELECT query from the result-set obtained by the first SELECT query and then return the filtered results from the first.
```sql
SELECT name FROM Students /* Fetch names from students */ MINUS /* that aren't present in contacts */ SELECT name FROM Contacts;
```
# Intersect
- Combines the result-set fetched by the two SELECT statements where records from one match the other and then returns this intersection of result-sets.
```sql
SELECT name FROM Students /* Fetch names from students */ 
INTERSECT /* that are present in contacts as well */ 
SELECT name FROM Contacts;
```
# Cursor
- A database cursor is a control structure that allows for the traversal of records in a database. Cursors, in addition, facilitates processing after traversal, such as retrieval, addition, and deletion of database records. They can be viewed as a pointer to one row in a set of rows.
	1.  **DECLARE** a cursor after any variable declaration. The cursor declaration must always be associated with a SELECT Statement.
	2.  Open cursor to initialize the result set. The **OPEN** statement must be called before fetching rows from the result set.
	3.  **FETCH** statement to retrieve and move to the next row in the result set.
	4.  Call the **CLOSE** statement to deactivate the cursor.
	5.  Finally, use the **DEALLOCATE** statement to delete the cursor definition and release the associated resources.
```sql
DECLARE @name VARCHAR(50) /* Declare All Required Variables */ 
DECLARE db_cursor CURSOR FOR /* Declare Cursor Name*/ 
SELECT name FROM myDB.students 
WHERE parent_name IN ('Sara', 'Ansh') 
OPEN db_cursor /* Open cursor and Fetch data into @name */ 
FETCH next FROM db_cursor 
INTO @name CLOSE db_cursor /* Close the cursor and deallocate the resources */ 
DEALLOCATE db_cursor
```
# Relationships
- One-to-One
- One-to-Many and Many-to-One
- Many-to-Many
- Self-Referencing relationships: Used when a table needs to define a relationship with itself
# Alias
- Keyword `as`
# View
- Virtual table based on the result-set of an SQL statement
# Normalization
- Normal forms are used to eliminate or reduce redundancy in database
	- 1st form: **single value attribute**
	- 2nd form: **no partial dependency**, no non-prime attribute (attributes which are not part of any candidate key) is dependent on any proper subset of any candidate key of the table.
	- 3rd form: **Transitive dependency –** If A → B and B → C are two FDs, then A → C is called transitive dependency.
# Truncate
- Command is used to delete all the rows from the table and free the space containing the table.
```sql
TRUNCATE TABLE Candidates;
```
# Delete
- Statement is used to delete rows from a table.
```sql
DELETE FROM Candidates WHERE CandidateId > 1000;
```
# Drop
- Delete table from database
`Drop table table_name`
# Aggregate ADN Scalar functions
- Aggregate function performs operations on a collection of values to return a single scalar value. Aggregate functions are often used with the GROUP BY and HAVING clause of the SELECT statement.
	- AVG
	- COUNT
	- MIN
	- MAX
	- SUM
	- FIRST
	- LAST
- Scalar function returns a single value based on the input value
	- LEN
	- UCASE: Converts a collection of string values into uppercase characters
	- LCASE: Opposite of UCASE
	- MID: Extract substring from a collection of string values in a table
	- CONCAT: Concatenates 2 or more strings
	- RAND
	- ROUND
	- NOW
	- FORMAT
# Stored Procedure
- It includes Transact-SQL, and it is saved in database. Users can call it to use.
- Transact-SQL is based on SQL, it is a programming language used to go between database and application.
- Advantages:
	- Module
	- Faster execution time and reduce bandwidth load because we can call 1 stored procedure instead of many queries
	- Security: by providing stored procedure unauthorized user can access allowed function without alter integrity of the security
# Pattern matching
![[Pasted image 20220315233259.png]]
- `%`
```sql
SELECT * FROM students WHERE first_name LIKE 'K%'
```
- `NOT`
```sql
SELECT * FROM students WHERE first_name NOT LIKE 'K%'
```
  - Use `%` twice
```sql
SELECT * FROM students WHERE first_name LIKE '%Q%'
```
- Use `_` to match pattern at a specific position
```sql
SELECT * FROM students WHERE first_name LIKE '__K%'
```
  - Matching patterns for a specific length
```sql
SELECT * /* Matches first names with three or more letters */ 
FROM students 
WHERE first_name LIKE '___%'
 
SELECT * /* Matches first names with exactly four characters */ 
FROM students 
WHERE first_name LIKE '____'
```
## Grouping
^1e5537
- Indicates whether a specified column expression in a GROUP BY list is aggregated or not. GROUPING returns 1 for aggregated or 0 for not aggregated in the result set. GROUPING can be used only in the SELECT `<select>` list, HAVING, and ORDER BY clauses when GROUP BY is specified.
## Rollup
^4874b1
- The rollup is an extension of the `GROUP BY` clause, allows you to include extra rows that represent the subtotal, which are commonly referred to as super-aggregate rows, along with the grand total row.
## Cube
^c8ce95
- Similar to [[SQL#^4874b1|rollup]], allows you to generate subtotals. In addition, the extension will generate subtotals for all combinations of grouping columns specified in the `GROUP BY` clause.