# Pivoting a result set into one row
^a6f205
- Problem: you want to take values from groups of rows and turn those values into columns in a single row per group.
- Solution: transpose the result set using CASE and the aggregate function SUM. When using CASE expression, we must know how many columns returned.
```SQL
-- group of rows
SELECT DEPTNO, COUNT(EMPNO) FROM EMP GROUP BY DEPTNO
-- group of columns
SELECT SUM(CASE WHEN DEPTNO = 10 THEN 1 ELSE 0 END) AS DEPTNO_10,
        SUM(CASE WHEN DEPTNO = 20 THEN 1 ELSE 0 END) AS DEPTNO_20,
        SUM(CASE WHEN DEPTNO = 30 THEN 1 ELSE 0 END) AS DEPTNO_30
FROM EMP
```
# Pivoting a result set into multiple rows
- Problem: you want to turn rows into columns by creating a column corresponding to each of the values in a single given column. However, you need multiple rows of output.
- Solution: use the rankning function `ROW_NUMBER OVER` to make each combination unique. Pivot the result set using a CASE expression and the aggregate function `MAX` while grouping on the value returned by the window function. The technique of using `ROW_NUMBER OVER` to create unique combinations of rows is extremely useful for formatting query result.
```sql
-- group of rows
SELECT JOB, ENAME FROM EMP

-- group of columns
select 
  max(case when job = 'CLERK' then ename else null end) as clerks, 
  max(case when job = 'ANALYST' then ename else null end) as analysts, 
  max(case when job = 'MANAGER' then ename else null end) as mgrs, 
  max(case when job = 'PRESIDENT' then ename else null end) as prez, 
  max(case when job = 'SALESMAN' then ename else null end) as sales 
from 
  (
    select job, ename, 
      row_number() over(
        partition by job 
        order by 
          ename
      ) rn 
    from emp
  ) x 
group by rn
```
# Reverse of [[Chapter 12 - Reporting and reshaping#^a6f205|Pivoting a result set into one row]]
```sql
select dept.deptno, 
  case dept.deptno 
    when 10 then emp_cnts.deptno_10 
    when 20 then emp_cnts.deptno_20 
    when 30 then emp_cnts.deptno_30 end 
  as counts_by_dept 
from 
  (
    emp_cnts cross 
    join (
      select deptno 
      from dept 
      where deptno <= 30
    ) dept
  )
```
# Reverse pivoting a result set into one column
- Problem: you want to return all columns from a query as just 1 column.
- Solution: the key is to use CTE combine with Cartesian product to return rows and the window function `ROW_NUMBER OVER` to rank each row.
```sql
with four_rows (id) as (
  select 1 
  union all 
  select id + 1 
  from four_rows 
  where id < 4
), 
x_tab (ename, job, sal, rn) as (
  select e.ename, e.job, e.sal, 
    row_number() over(
      partition by e.empno 
      order by e.empno
    ) 
  from emp e 
    join four_rows on 1 = 1
) 
select 
  case rn when 1 then ename when 2 then job when 3 then cast(
    sal as char(4)
  ) end emps 
from 
  x_tab
```
# Supressing repeating values from a result set
- Problem: when 2 tows have the same value in a column, you want to display that value only once.
- Solution: use `LAG OVER` function to access previos rows.
```sql
select
case when
    lag(deptno)over(order by deptno) = deptno then null
    else deptno end DEPTNO
, ename
from emp
```
# Creating a predefined number of buckets
- Problem: you want to organize your data into a fixed number of buckets.
- Solution: use `NTILE OVER` function to organizes ordered set into the number of buckets you specify.
```sql
select NTILE(4) over (order by empno) grp, empno, ename from emp
```
# Returning non-group by columns
- Problem: while executing a GROUP BY query, and you want to return columns in your select list that are not also listed in your GROUP BY clause. This is not normally possible, as such ungrouped columns would not represent a single value per row.
-  Solution: depending on the result, usually with non-group columns invole with calculation, you can use `MIN OVER` and `MAX OVER` function.
# Calculating simple subtotals
- Solution: use the [[SQL#^4874b1|rollup]] extension to the `GROUP BY` clause solves this problem perfectly. With the help of [[SQL#^1e5537|grouping]] you can find you which row is grouped or not.
```sql
select case grouping(job) 
		when 0 then job 
		else 'total' end, 
	sum(sal) sal 
from emp group by rollup(job)
```
# Calculating subtotal for all possible expression combinations
- Problem: example use case you want to find the sum of all salaries by DEPTNO, and by JOB, for every JOB/DEPTNO combination.
- Solution: take advantages of [[SQL#^c8ce95|cube]] extension added to `GROUP BY`.
```sql
select deptno, job, 
  case cast(grouping(deptno) as char(1))
  + cast(grouping(job) as char(1)) 
  when '00' then 'TOTAL BY DEPT AND JOB' 
  when '10' then 'TOTAL BY JOB' 
  when '01' then 'TOTAL BY DEPT' 
  when '11' then 'GRAND TOTAL FOR TABLE' end category, 
  sum(sal) sal 
from emp 
group by deptno, job with cube 
order by 
  grouping(job), 
  grouping(deptno)
```
# Note
```sql
select ename, deptno, 
  count(*) over(partition by deptno) deptno_cnt, 
  job, 
  count(*) over(partition by job) job_cnt, 
  count(*) over() total 
from 
  emp
```
- Keep in mind that window functions are applied after the WHERE clause. If you were to filter the result set in some way, for example, excluding all employees in DEPTNO 10, the value for TOTAL would not be 14—it would be 11. To filter results after window functions have been evaluated, you must make your windowing query into an inline view and then filter on the results from that view