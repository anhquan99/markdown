## Paginating throught a result set
- Solution: Use `ROW_NUMBER OVER` function to impose order and specify the window of records that you want returned. 
```sql
select sal 
from 
  (
    select row_number() over (order by sal) as rn, sal 
    from emp
  ) x 
where rn between 1 and 5
```
## Select the top n records
- Solution: Either use the `LIMIT` or `DENSE_RANK OVER` function to select n records.
```sql
select ename, sal
from
(
    select ename, sal,
    dense_rank() over (order by sal desc) dr
    from emp
) x where dr <= 5 
```
## Investigating future rows
- Solution: Use `LEAD OVER` function to access next row.
## Finding knight values
- Problem: you want result a result set that containes each employee's name, the department they work in, their salary, the date they were hired, and the salary of the last employee hired, in each department.
  ![[Pasted image 20230217221012.png]]
- Solution: use a case expression in a subquery to return the SAL of the last employee hired in each DEPTNO; for all other salaries, return 0. Use the window function `MAX OVER` in ther outer query to return the nonzero SAL for each employee's department.
```sql
select deptno, ename, sal, hiredate, max(latest_sal) over(partition by deptno) latest_sal 
from 
  (
    select deptno, ename, sal, hiredate, 
      case 
      when hiredate = max(hiredate) over(partition by deptno) then sal 
      else 0 end latest_sal 
    from 
      emp
  ) x 
order by 1, 4 desc
```
## Generating simple forecasts
- Problem: based on current data, you want to return additional rows and columns representing future actions.
- Solution: the key is to use a Cartesian product to generate two additional rows for each order and then simply use CASE expression to create the required column values.
- Data:
  ![[Pasted image 20230217221941.png]]
- Result:
  ![[Pasted image 20230217222000.png]]
```sql
with nrows(n) as (
  select 1 from t1 
  union all 
  select n + 1 
  from nrows 
  where n + 1 <= 3
) 
select 
  id, 
  order_date, 
  process_date, 
  case when nrows.n >= 2 then process_date + 1 else null end as verified, 
  case when nrows.n = 3 then process_date + 2 else null end as shipped 
from 
  (
    select 
      nrows.n id, 
      getdate()+ nrows.n as order_date, 
      getdate()+ nrows.n + 2 as process_date 
    from 
      nrows
  ) order, nrows
order by 1
```