## [[Setup DB]]
## Walk the string
- Problem: given a string contains ID with csv (Comma-separated values). How to query table from the string.
- Solution:
	1. Add a comma in the head of the string.
	2. Use a pivot table to iterate through the string.
	3. Substring with iterator index of pivot table.
	4. Select result from step 3 where length of result is > 1 and substring start with a comma.
- Example:
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