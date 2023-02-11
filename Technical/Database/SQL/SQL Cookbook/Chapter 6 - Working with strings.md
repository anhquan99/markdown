## Walk the string
- <mark style="background: #ADCCFFA6;">Problem:</mark> given a string contains ID with csv (Comma-separated values). How to query table from the string.
- <mark style="background: #ADCCFFA6;">Solution:</mark>
	1. Add a comma in the head of the string.
	2. Use a pivot table to iterate through the string.
	3. Substring with iterator index of pivot table.
	4. Select result from step 3 where length of result is > 1 and substring start with a comma.
- <mark style="background: #ADCCFFA6;">Example:</mark>
```sql
select empno, ename, sal, deptno 
    from emp 
    where empno in (
        select 
            -- step 4
            substring(
                c, 
                2, 
                -- need to -2 because the end position of the string
                charindex(',', c, 2)-2
            ) as empno 
        from (
            select
                -- step 3
                substring(
                    csv.emps, 
                    iter.pos, 
                    len(csv.emps)
                ) as c 
            from 
            (
                select 
                -- step 1
                ',' + '7654,7698,7782,7788' + ',' as emps from t1
            ) csv, 
            -- step 2
            (
                select 
                id as pos 
                from 
                t100
            ) iter where 
            iter.pos <= len(csv.emps)
        ) x 
    where 
    len(c) > 1 
    and substring(c, 1, 1) = ','
)
```
## Case sensitive and insensitive
- In SQL Server 2017 the default string comparision is case insensitive.
- If you want to compare case sensitive then use the `COLLATE + $Collate_type`.
## Create a delimited list from table rows
- <mark style="background: #ADCCFFA6;">Problem:</mark> you want to return table rows as values in a delimited list.
- <mark style="background: #ADCCFFA6;">Solution:</mark> use `string_agg()` function for SQL Server
```sql
select deptno,
        string_agg(ename, ',') within GROUP (order by empno) as emps
from emp
group by deptno
```
## Comparing strings by sound
- Use `SOUNDEX` function.