# Chapter 7: Working With Numbers

* Aggregate functions (like `avg` ignore `NULL`s) - to incorporate, need to convert `NULL`s to `0`s for example (when calculating averages)
* `COUNT(*)` is really countings rows (regardless of the column value) which is why rows containing both `NULL` and non-`NULL` values are counted 

## Calculating A `mode`

* The `mode` is the element in a set of data that appears most frequently
* Query finds the mode of the salares in department number 20
* The subquery returns the number of times each salary occurs
  * The `ALL` opeartor returns `true` if a salary group has a count that is `>=` the largest value in the subquery (i.e. the number of times each salary occurs)

```sql
SELECT
  sal
FROM emp
WHERE deptno = 20
GROUP BY sal
HAVING COUNT(*) >= ALL(
  SELECT COUNT(*)
  FROM emp
  WHERE deptno = 20
  GROUP BY SAL
)
```

## Calculating A `median`

* `median` is the middle member of a set of ordered elements
* Query self-joins, which returns Cartesian production of all employees with all other employees
  * The `HAVING` clause counts the number of times the first table's employee salary is greater than the second table's employee salary
  * If the count where the first table's employee salary is equal to the second table's employee salary is greater than or equal to the count of higher salary, then that salary is the median salary
  * Another way of thinking about this is that the `group by` + `case` statement basically calculates the number of duplicate salaries there are for employees 
    * Even though the `group by` will distinctly group these duplicate salaries, the cross-product will still produce `N` rows for the number of duplicate employee salary value
    * If there are _no_ duplicate salaries, then the median salary is the one where `abs(sum(sign(e.sal - d.sal)))` is `0` - where the number of values greater than the salary value is equal to the number of values less than the salary value
    * However, if there _are_ duplicate salaries, then return all salaries where the count of duplicate values is `gte` the number of values that are greater / less than the current value


```sql
select avg(sal)
 from (
  select e.sal
   from emp e, emp d
  where e.deptno = d.deptno
    and e.deptno = 20
  group by e.sal
  having sum(case when e.sal = d.sal then 1 else 0 end) >= abs(sum(sign(e.sal - d.sal)))
)
```

## Determining The Percentage Of A Total

* Determine what percentage of all salaries are the salaries in department number 10
* The `CASE` statement only returns salaries from department `10`
* Then they are summed and divided by the sum of all the salaries
* `NULL`s are ignored by aggregates, so an `ELSE` clause is not needed in the `CASE`

```sql
SELECT
  (SUM(CASE WHEN deptno = 10 THEN sal END) / SUM(SAL)) * 100 AS deptno_10_percentage
FROM emp
```

## Aggregating Nullable Columns

* Use `COALESCE` to convert `NULL`s to `0` so they will be included in an aggregation
