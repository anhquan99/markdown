## Locating a range of consecutive values
- Solution: use `LEAD OVER` function to examine other rows without performing a self-join.
## Locating the beginning and end of a range of consecutive values
- Problem: you want to get data range that consecutive row by row.
- Solution: use `LAG OVER` function.
- Data:
  ![[Pasted image 20230217180119.png]]
  ```sql
select proj_grp, min(proj_start), max(proj_end) 
from 
  (
    select proj_id, proj_start, proj_end, 
      sum(flag) over(
        order by 
          proj_id
      ) proj_grp 
    from 
      (
        select proj_id, proj_start, proj_end, 
          case when lag(proj_end) over(
            order by 
              proj_id
          ) = proj_start then 0 else 1 end flag 
        from 
          V
      ) alias1
  ) alias2 
group by 
  proj_grp
```
## Generating consecutive numberic values
- Problem: you want to generate consecutive numberic values like pivolt table, ...
- Solution: use CTE to iterate values and insert into tables.
```sql
with x (id)
as (
    select 1 union all
    select id + 1
    from x
    where id + 1 <= 10
)
select * from x
```