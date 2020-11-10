# Appendix A: Window Function Refresher

* When you use `GROUP BY` in a query, each row in a result set is a group and represents one or more rows with the same values in one or more columns that you specify
* For each member of the result set, the aggregate `COUNT` function must return a value greater than `0`
* Groups must have at least one member / row - they cannot be empty and thus can also not be created from an empty table
* Groups must also be distinct

## Grouping Values

* Imagine a table with a single `name` column that contains text values referring to names of fruits
* Assume the column is nullable and that `5` `null` values have been added to the table
* The following query will actually output a count of `0` for the grouped `NULL` values

```sql
SELECT 
  COALESCE(name, 'NULL') AS name,
  COUNT(name) AS cnt
FROM fruits
GROUP BY name
```

* This is because `COUNT(name)` will ignore `NULL` values if any exist in the column passed to them
  * Instead, use `COUNT(*)` which will count rows rather than actual column values, and won't "filter out" `NULL` values

```sql
SELECT
  COALESCE(name, 'NULL') AS name,
  COUNT(*) AS cnt
FROM fruits
GROUP BY name
```

* Any item in the `SELECT` that is not used as an argument to an aggregate function must be part of your group
  * Exceptions to this are constants, scalar values returned by user-defined functions, window functions, and non-correlated scalar subqueries
  * More specifically, items in a `SELECT` that can potentially change the group or change the value returned by an aggregate function must be included in the `GROUP BY` clause

## Windowing

* Window functions perform an aggregation on a defined set / group of rows but can return multiple values per group

```sql
SELECT
  ename,
  deptno,
  COUNT(*) OVER() AS cnt
FROM emp
ORDER BY 2
```

* The previous query uses a window function to access aggregated data (the total count of employees) from a set of detail rows (one row per employee)
* `OVER` indicates that `COUNT` will be treated as a window function
  * In general, the SQL standard allows all aggregate functions to be window functions
* `OVER` can take some arguments, but absent any arguments, it will look at all rows in the result set, which is why the same number (which represents the total number of employees) is returned for each row in the result set
* Window functions are performed as the last step in SQL processing prior to the `ORDER BY` clause
  * Window functions will perform computations after `WHERE` and `GROUP BY` clauses are evaluated

### Partitions

* A `PARTITION BY` clause is kind've like a moving `GROUP BY`
* `COUNT(*) OVER(PARTITION BY deptno) AS cnt` in previous query would return the counts of every employee in each department for each row in the employees table
  * So each row in the employees table would return a count of all the employees in the department that the current employee is in

```sql
# Essentially same as this, just more efficient
SELECT
  e.ename,
  e.deptno,
  (
    SELECT COUNT(*) FROM emp AS d WHERE e.deptno = d.deptno
  ) AS cnt
FROM emp AS e
ORDER BY 2
```

* Can combine partitioning by different columns in the same `SELECT`
* `COUNT(*) OVER(PARTITION BY deptno)` + `COUNT(*) OVER(PARTITION BY job)` would return a result set where employees in the same department have the same value for the department count while employees who have the same job position have the same value for the job count
* Similar to `GROUP BY`, `PARTITION BY` lumps all `NULL`s into one group or partition, so when using aggregate functions, need to be aware that aggregate functions ignore `NULL` values and if you need to count `NULL` values, use `COUNT(*)` which will count rows

### Incorporating Order

* Adding an `ORDER BY` in the `OVER` clause of the window function determines how the rows in the partition are ordered and what rows are included in the computation
* This query sums and computes a running total of salaries for employees in `DEPTNO=10`
  * Note that `total1` and `total2` will return the same results since "partitioning by deptno" when only one department is selected is the same as summing salaries over the entire result set
  * `running_total` is ordered by `hiredate` which means that the value will be the sum of the cumulative total of salaries up to that employee row (where the rows are ordered by `hiredate`)
  * In this way, the `order by hiredate` specifies a "moving" / "sliding" window similar to a `RANGE BETWEEN` clause

```sql
select 
   deptno,
   ename,
   hiredate,
   sal,
   sum(sal)over(partition by deptno) as total1,
   sum(sal)over() as total2,
   sum(sal)over(order by hiredate) as running_total
from emp
where deptno=10
```

* This default behavior by `order by` can be more explicitly denoted by the following SQL
  * This tells the query to sum all rows starting from the current row and including all prior rows (which is determined by the `ORDER BY`, so ordering by `hiredate`)

```sql
sum(sal) over(order by hiredate range between unbounded preceding and current row) as running_total
```


### Framing Clause

* The previous "framing clause" / `RANGE BETWEEN` clause started with the first employee hired, `CLARK`
* `CLARK`'s salary was `2450`, so including all employees hired before `CLARK` (which there are none) compute the sum of salaries (`2450`)
* Next employeed based on `hiredate` is `KING`; compute the salary sum starting with the current row (`5000` for `KING) and all prior rows (i.e. employees hired before `KING`)
  * In this case, it's just `CLARK`'s salary + `KING`'s salary so `7450`
* Same thing for the final employee's `running_total` value, which will just be the sum of their salary + the sum of the previous salaries
* Different framing clauses allow the definition of different "sub-windows" of data

```sql
sum(sal)over(order by hiredate
          rows between 1 preceding
           and current row) as run_total2,
sum(sal)over(order by hiredate
         range between current row
           and unbounded following) as run_total3,
sum(sal)over(order by hiredate
          rows between current row
           and 1 following) as run_total4
```

* `1 PRECEDING` means that the frame will begin with the row immediately _before_ the current row and then the end of the range is the current row
* `range between current row and unbounded following` is the summation of the current row and all subsequent rows
* `range between current row and 1 following` is the summation of the current row and the next row
