# Generating a running total
- Problem: you want to calculate a running total of values in a column like the total money of each month running from beginning of the year to the end of the year.
- Solution: use `SUM($COLUMN_NAME) OVER (ORDER BY $COLUMN_NAME, $COLUMN_NAME1, ...)`.
```sql
select  ename, 
        sal, 
        sum(sal) over (order by sal, empno) as running_total
        from emp
```
# DENSE_RANK
- Returns the rank of each row within a result set of partition, with no gaps in the ranking values. The rank of a specific row is one plus the number of distinct rank values that come before that specific row.
```sql
DENSE_RANK ( ) OVER ( [ <partition_by_clause> ] < order_by_clause > )
```
# ROW_NUMBER
- Numbers the output of a result set. More specifically, return the sequential number of a row within a partition of a result set, starting at 1 for the first row in each partition.
- `ROW_NUMBER` and `RANK` are similar.
```sql
# syntax
ROW_NUMBER ( )   
    OVER ( [ PARTITION BY value_expression , ... [ n ] ] order_by_clause )
# example
SELECT 
  ROW_NUMBER() OVER(ORDER BY name ASC) AS Row#,
  name, recovery_model_desc
FROM sys.databases 
WHERE database_id < 5;
```
# PARTITION BY
- The `PARTITION BY` clause is a subclause of the `OVER` clause. It divides a query's result into partitions. The windows function is operated on each partition separately and recalculate for each partition.
- `PARTITION BY` vs `GROUP BY`:
	- The `GROUP BY` clause is used often in conjunction with an aggregate function, such as `SUM()`, `AVG()`. It reduces number of rows returned by rolling them up and calculating each group.
	- The `PARTITION BY` clause divides the result set into partitions and changes how the window function is calculated. It doesn't reduce the number of rows returned.
	- The `GROUP BY` clause is aggregate, while the `PARTITION BY` clause is analytic
# WITHIN GROUP
- A clause used with some aggregate and analytic function where you want an order-related result.
# Calculate median
- Problem: you want to calculate the median value for a column of numeric values.
- Solution: use `PERCENTILE_CONT` function.
```sql
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY SAL) OVER()
FROM EMP WHERE DEPTNO = 20
```
# Changing values in a running total
- Problem: you want to modify the values in a running total depending on the values in another column.
- Solution: use `case-when` for specific cases with calculate function.
```sql
create view V (id,amt,trx)
as
select 1, 100, 'PR' union all
select 2, 100, 'PR' union all
select 3, 50, 'PY'  union all
select 4, 100, 'PR' union all
select 5, 200, 'PY' union all
select 6, 50, 'PY' 

select 
  case when trx = 'PY' then 'PAYMENT' else 'PURCHASE' end trx_type, 
  amt, 
  sum(
    case when trx = 'PY' then - amt else amt end
  ) over (
    order by 
      id, 
      amt
  ) as balance 
from 
  V
```
# Finding outliers using the median absolute deviation
- Problem: you want to identify values in your data that may be suspect. A comm way to detect outliers, taught in many statistics courses aimed at non-statisticians, is to calculate the standard deviation of the data and decide that data points more than three standard deviations (or some other similar distance) are outliers. However, this method can misidentify outliers if the data don’t follow a normal distribution, especially if the spread of data isn’t symmetrical or doesn’t thin out in the same way as a normal distribution as you move further from the mean. 
- Solution: First find the median of the values using the recipe for finding the median from earlier in this chapter. You will need to put this query into a CTE to make it available for further querying. The deviation is the absolute difference between the median and each value; the median absolute deviation is the median of this value, so we need to calculate the median again.
- Discussion: In each case, the recipe follows a similar strategy. First we need to calculate the median, and then we need to calculate the median of the difference between each value and the median, which is the actual median absolute deviation. Finally, we need to use a query to find the ratio of the deviation of each value to the median deviation. At that point, we can use the outcome similarly to the standard deviation. For example, if a value is three or more deviations from the median, it can be considered an outlier, to use a common interpretation. As mentioned earlier, the benefit of this approach over the standard deviation is that the interpretation is still valid even if the data doesn’t display a normal distribution. For example, it can be lopsided, and the median absolute deviation will still give a sound answer.
```sql
with median (median) as (
  select 
    distinct percentile_cont(0.5) within group(
      order by 
        sal
    ) over() 
  from 
    emp
), 
Deviation (Deviation) as (
  Select 
    abs(sal - median) 
  from 
    emp 
    join median on 1 = 1
), 
MAD (MAD) as (
  select 
    DISTINCT PERCENTILE_CONT(0.5) within group(
      order by 
        deviation
    ) over() 
  from 
    Deviation
) 
select 
  abs(sal - MAD)/ MAD, 
  sal, 
  ename, 
  job 
from 
  MAD 
  join emp on 1 = 1
```
# Finding anomalies using Benford's law
- Problem: One way to detect situations where there are anomalous data but no obvious outliers is to look at the frequency of digits, which is usually expected to follow Benford’s law. Although using Benford’s law is most often associated with detecting fraud in situations where humans have added fake numbers to a data set, it can be used more generally to detect data that doesn’t follow expected patterns. For example, it can detect errors such as duplicated data points, which won’t necessarily stand out as outliers.
- Solution: To use Benford’s law, you need to calculate the expected distribution of digits and then the actual distribution to compare. Although the most sophisticated uses look at first, second, and combinations of digits, in this example we will stick to just the first digits. You compare the frequency predicted by Benford’s law with the actual frequency of your data. Ultimately you want four columns—the first digit, the count of how many times each first digit appears, the frequency of first digits predicted by Benford’s law, and the actual frequency.
- Discussion: 
	- Because we need to make use of two different counts—one of the total rows, and another of the number of rows containing each different first digit—we need to use a CTE. Strictly speaking, we don’t need to put the expected Benford’s law results into a separate query within the CTE, but we have done so in this case as it allows us to identify the digits with a zero count and display them in the table via the right join. It’s also possible to produce the FirstDigits count in the main query, but we have chosen not to improve readability through not needing to repeat the LEFT(CAST… expression in the GROUP BY clause. 
	- The math behind Benford’s law is simple: $Expected frequency= log((d+1)/10)$ 
	- We can use the T10 pivot table to generate the appropriate values. From there we just need to calculate the actual frequencies for comparison, which first requires us to identify the first digit. Benford’s law works best when there is a relatively large collection of values to apply it to, and when those values span more than one order of magnitude (10, 100, 1,000, etc.). Those conditions aren’t entirely met here. At the same time, the deviation from expected should still make us suspicious that these values are in some sense made-up values and worth investigating further.
```sql
with FirstDigits (FirstDigit) as (
  select 
    left(
      cast(SAL as CHAR), 
      1
    ) as FirstDigit 
  from 
    emp
), 
TotalCount (Total) as (
  select 
    count(*) 
  from 
    emp
), 
ExpectedBenford (Digit, Expected) as (
  select 
    value, 
    (
      log10(value + 1) - log10(value)
    ) as expected 
  from 
    t10 
  where 
    value < 10
) 
select 
  count(FirstDigit), 
  Digit, 
  coalesce(
    count(*)/ Total, 
    0
  ) as ActualProportion, 
  Expected 
From 
  FirstDigits 
  Join TotalCount 
  Right Join ExpectedBenford on FirstDigits.FirstDigit = ExpectedBenford.Digit 
group by 
  Digit 
order by 
  Digit;
```

```ad-note
- `NULL` should be consider when working with number, usually the database will pass the data if `NULL`.
- `COALESCE` can be used for `NULL` value.
```