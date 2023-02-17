## Determining the number of business days between two dates
- <mark style="background: #ADCCFFA6;">Problem:</mark> Given 2 dates, you want to find how many working days are between them, including the two dates themselves. The business day is defined as any day that is not Saturday or Sunday. 
- <mark style="background: #ADCCFFA6;">Solution:</mark> Use the pivot table T500 to generate the required number of rows (days) between the 2 dates, and count each day that is not a weekend. Use the `DATENAME()` to obtain the weekday name of each date:
	1. Return the days between the start date and end date (inclusive).
	2. Count how many days (rows) there are, excluding weekends.
```sql
select 
  sum(
    case when datename(dw, jones_hd + t500.id - 1) in ('SATURDAY', 'SUNDAY') then 0 else 1 end
  ) as days 
from 
  (
    selectmax(
      case when ename = 'BLAKE' then hiredate end
    ) as blake_hd, 
    max(
      case when ename = 'JONES' then hiredate end
    ) as jones_hd 
    from 
      emp 
    where 
      ename in ('BLAKE', 'JONES')
  ) x, 
  t500 
where 
  t500.id <= datediff(day, jones_hd - blake_hd)+ 1

```
## Determining the number of months or years between two dates
- <mark style="background: #ADCCFFA6;">Solution:</mark> Use `DATEDIFF()`.
## Determining the number of seconds, minutes or hours between two dates.
- <mark style="background: #ADCCFFA6;">Solution:</mark> Use `DATEPART()` with `DATEDIFF()`
## Counting the occurrences of weekdays in a year
- <mark style="background: #ADCCFFA6;">Problem:</mark> you want to count the number of times each weekday occurs in one year.
- <mark style="background: #ADCCFFA6;">Solution:</mark>
	1. Generate all possible dates in the year.
	2. Format the dates such that they resolve to the name of their respective weekdays.
	3. Count the occurrence of each weekday name.
```sql
-- Use the recursive WITH to avoid the need to SELECT against a table with at least 366 rows. Use the DATENAME function to obtain the weekday name of each date, and then count the occurrence of each name.
with x(start_date, end_date) as (
  select start_date, dateadd(year, 1, start_date) end_date 
  from 
    (
      select cast(cast(year(getdate()) as varchar) + '-01-01' as datetime) start_date 
      from t1
    ) tmp 
  union all 
  select dateadd(day, 1, start_date), end_date 
  from 
    x 
  where 
    dateadd(day, 1, start_date) < end_date
) 
select 
  datename(dw, start_date), 
  count(*) 
from 
  x 
group by 
  datename(dw, start_date) OPTION (MAXRECURSION 366)
```
## Determining the date difference between the current record and the next record
- <mark style="background: #ADCCFFA6;">Problem:</mark> you want to determine the difference in days between two dates (specifically dates stored in two different rows). For example, for every employee in DEPTNO 10, you want to determine the number of days beween the day they were hired and the next employee (can be in another department) was hired.
- <mark style="background: #ADCCFFA6;">Solution</mark>: Use `LEAD() OVER()` function to access the next row (hire date of employee).
```sql
select x.ename, x.hiredate, x.next_hd, datediff(day, x.hiredate, x.next_hd) as diff 
from 
  (
    select deptno, ename, hiredate, lead(hiredate) over(order by hiredate) as next_hd 
    from 
      emp e
  ) x 
where x.deptno = 10
```
