## Is leap year
- Solution: combine string and then try to convert date if existed.
```sql
select case day(try_convert(date, concat(year(getdate()), '/02/29'),111)) 
		when null then 'true' 
		else 'false' end
```
## Determining all dates for a particular weekday throughout a year
- Problem: get all the date with specific day in the week.
- Solution: use CTE to iterate all day in a year and get all the day fall into that day in the week.
```sql
with x (dy, yr) as (
  select dy, year(dy) yr 
  from 
    (
      select getdate()- datepart(dy, getdate())+ 1 dy 
      from t1
    ) tmp1 
  union all 
  select dateadd(dd, 1, dy), yr 
  from x 
  where year(dateadd(dd, 1, dy)) = yr
) 
select x.dy 
from x 
where datename(dw, x.dy) = 'Friday' option (maxrecursion 400)
```
## Create a calendar
```sql
with x(dy, dm, mth, dw, wk) as (
  select dy, day(dy) dm, datepart(m, dy) mth, datepart(dw, dy) dw, 
    case when datepart(dw, dy) = 1 then datepart(ww, dy)-1 else datepart(ww, dy) end wk 
  from 
    (
      select dateadd(day, - day(getdate())+ 1, getdate()) dy 
      from t1
    ) x 
  union all 
  select dateadd(d, 1, dy), day(dateadd(d, 1, dy)), mth, datepart(dw, dateadd(d, 1, dy)), 
    case when datepart(
      dw, 
      dateadd(d, 1, dy)
    ) = 1 then datepart(
      wk, 
      dateadd(d, 1, dy)
    ) -1 else datepart(
      wk, 
      dateadd(d, 1, dy)
    ) end 
  from x 
  where datepart(m, dateadd(d, 1, dy)) = mth
) 
select 
  max(case dw when 2 then dm end) as Mo, 
  max(case dw when 3 then dm end) as Tu, 
  max(case dw when 4 then dm end) as We, 
  max(case dw when 5 then dm end) as Th, 
  max(case dw when 6 then dm end) as Fr, 
  max(case dw when 7 then dm end) as Sa, 
  max(case dw when 1 then dm end) as Su 
from 
  x 
group by 
  wk 
order by 
  wk
```