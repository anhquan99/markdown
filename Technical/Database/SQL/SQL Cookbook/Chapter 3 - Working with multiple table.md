## Union
- Operator is used to combine rows from multiple tables.
- With each column of each table they must have a similar type.
- Result will be returned in distinct.
- `Union all` will not returned distinct result the work around to have distinct result is using `distinct`.
## Equi join
^abdd24
- Performs a join against or matching columns values of associated tables.
- Equi join only have an equality operator in the join condition
- Equi join can be an inner, left , right  join.
```sql
select e.ename, d.loc from emp e, dept d where e.DETNO = d.DETNO and e.DETNO = 10
# equal to
select e.ename, d.loc from emp e inner join dept d on e.DETNO = d.DETNO and e.DETNO = 10
```
## INTERSECT 
- <mark style="background: #ADCCFFA6;">Problem:</mark> you want to find common rows between 2 tables, but there are multiple columns on which you can join.
- <mark style="background: #ADCCFFA6;">Solution:</mark> join tables. Alternatively, use the set operation `INTERSECT` to avoid performing a join and instead return the intersectioon (common rows) of the tables.
- When performing joins, you must consider the proper columns to join in order to return correct results. The set operator `INTERSECT` will return rows common to both row sources.
- ***"The `INNER JOIN` will return duplicates, if `id` is duplicated in either table. `INTERSECT` removes duplicates. The `INNER JOIN` will never return `NULL`, but `INTERSECT` will return `NULL`. The two are very different; `INNER JOIN` is an operator that generally matches on a limited set of columns and can return zero rows or more rows from either table. `INTERSECT` is a set-based operator that compares complete rows between two sets and can never return more rows than in the smaller table."***
- <mark style="background: #ADCCFFA6;">Example:</mark>
```sql
select ename, job, sal from v INTERSECT select ename, job, sal from emp
# the result above will be return differntly from the result below
select ename, job, sal from v INTERSECT select dname, loc, detno from dept
```
## EXCEPT
- <mark style="background: #ADCCFFA6;">Problem:</mark> you want to find values in one table are not in another table.
- <mark style="background: #ADCCFFA6;">Solution:</mark> 
	- Use built in operator `EXCEPT`, the operator will not return duplicates.
	- Use `NOT IN` operator.
- Discussion:
	```sql
	create table new_dept(deptno integer)
	insert into nullabletable values (10)
	insert into nullabletable values (50)
	insert into nullabletable values (null)
	
	select * from dept where detno not in (select detno from new_dept)
	```
	- The result from query above return no result because the `null` value from the `new_dept` table
- <mark style="background: #FF5582A6;">The truth table:</mark>

| OR  | T   | F   | N   |
| --- | --- | --- | --- |
| T   | T   | T   | T   |
| F   | T   | F   | N   |
| N   | T   | N   | N   |

| NOT |     |
| --- | --- |
| T   | F   |
| F   | T   |
| N   | N    |

| AND | T   | F   | N   |
| --- | --- | --- | --- |
| T   | T   | F   | N   |
| F   | F   | F   | F   |
| N   | N   | F   | N    |

```sql
# the OR operator with null
select deptno from dept where deptno in ( 10,50,null ) 
DEPTNO 
------- 
10 

select deptno from dept where (deptno=10 or deptno=50 or deptno=null) 
DEPTNO 
------- 
10

DEPTNO=10 
(deptno=10 or deptno=50 or deptno=null) 
= (10=10 or 10=50 or 10=null) 
= (T or F or N) = (T or N) 
= (T)

DEPTNO=20 
(deptno=10 or deptno=50 or deptno=null) 
= (20=10 or 20=50 or 20=null) 
= (F or F or N) 
= (F or N) 
= (N) 

DEPTNO=30 
(deptno=10 or deptno=50 or deptno=null) 
= (30=10 or 30=50 or 30=null) 
= (F or F or N) 
= (F or N) 
= (N) 

DEPTNO=40 
(deptno=10 or deptno=50 or deptno=null) 
= (40=10 or 40=50 or 40=null) 
= (F or F or N) 
= (F or N) 
= (N)
```

```sql
# the AND operator with null
select deptno
 from dept
 where deptno not in ( 10,50,null )

( no rows )

select deptno
 from dept
 where not (deptno=10 or deptno=50 or deptno=null)

( no rows )

DEPTNO=10
NOT (deptno=10 or deptno=50 or deptno=null)
= NOT (10=10 or 10=50 or 10=null)
= NOT (T or F or N)
= NOT (T or N)
= NOT (T)
= (F)

DEPTNO=20
NOT (deptno=10 or deptno=50 or deptno=null)
= NOT (20=10 or 20=50 or 20=null)
= NOT (F or F or N)
= NOT (F or N)
= NOT (N)
= (N)
```
- <mark style="background: #ADCCFFA6;">Example:</mark>
```sql
create view no40dept
as select * from dept where dept.DETNO = 40

select * from dept
EXCEPT
select * from no40dept
```