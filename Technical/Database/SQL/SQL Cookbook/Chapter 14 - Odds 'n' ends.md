## Creating cross-tab reports using SQL Server pivot operator
^de5c65
- Solution: use `PIVOT` function replace for [[Chapter 12 - Reporting and reshaping#^a6f205|Pivoting a result set into one row]].
```sql
select 
  [10] as dept_10, 
  [20] as dept_20, 
  [30] as dept_30, 
  [40] as dept_40 
from 
  (
    select deptno, empno 
    from emp
  ) driver pivot (
    count(driver.empno) for driver.deptno in ([10], [20], [30], [40])
  ) as empPivot
```
## Unpivot (Reverse [[Chapter 14 - Odds 'n' ends#^de5c65|Creating cross-tab reports using SQL Server pivot operator]])
- Solution: use `UNPIVOT` function.
```sql
select cnt, deptno 
from 
  (
    select 
      [10] as dept_10, 
      [20] as dept_20, 
      [30] as dept_30, 
      [40] as dept_40 
    from 
      (
        select deptno, empno 
        from 
          emp
      ) driver pivot (
        count(driver.empno) for driver.deptno in ([10], [20], [30], [40])
      ) as empPivot
  ) as new_driver UNPIVOT(
    cnt for deptno in (dept_10, dept_20, dept_30, dept_40)
  ) as un_pivolt
```
