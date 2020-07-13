# Chapter 3

## Stacking One Rowset atop Another

* `UNION ALL` combines rows from multiple row sources into one result set - the items in each `SELECT` must match in number and data type
  * `UNION ALL` will include duplicates, if they exist
  * `UNION` operator will _not_ include duplicates

## Finding Rows in Common Between Two Tables

* There's a view of all `CLERK` jobs with the employees name, job, and salary
  * But need to return the employee number, name, job, salary, and department number from the employees table that _also_ are the clerks from the view

```sql
CREATE VIEW v
  AS
  SELECT
    ename,
    job,
    sal
  FROM emp
  WHERE job = 'CLERK'
```

* Can solve by inner joining like

```sql
SELECT
  e.empno,
  e.ename,
  e.job,
  e.sal,
  e.deptno
FROM emp AS e, V
WHERE e.ename = v.ename
  AND e.job = v.job
  AND e.sal = v.sal
```

* Can also use `INTERSECT` + `IN`
  * Columns need to have the same datatype and duplicate rows will not be returned

```sql
SELECT
  empno,
  ename,
  job,
  sal,
  deptno
FROM emp
WHERE (ename, job, sal) IN (
  SELECT ename, job, sal FROM emp
  INTERSECT
  SELECT ename, job, sal FROM V
)
```

## Retrieving Values from One Table That Do Not Exist in Another

* Find values in the source table that do not also exist in a target table
  * Find which departments (if any) in the `DEPT` table do not exist in the `EMPT` table

```sql
SELECT
  deptno
FROM dept
EXCEPT
SELECT
  deptno
FROM emp
```

* `EXCEPT` is valid for `PostgreSQL` - it takes first result set and removes all rows found in second result set
  * Data type and list size of each `SELECT` must match
  * No duplicates are returned

* For `MySQL` need to use a subquery like

```sql
SELECT
  deptno
FROM dept
WHERE deptno NOT IN (
  SELECT deptno FROM emp
)
```

* However, need to consider `NULL`s when using `WHERE` + `IN` (which is effectively equivalent to a `WHERE` + `OR` for each value in the `IN`
  * `WHERE deptno IN (10, 50, null)` is effectively equivalent to `WHERE (deptno=10 OR deptno=50 OR deptno=null)`
  * `NOT IN` is also `OR`'d like `WHERE NOT (deptno=10 OR deptno=50 OR deptno=null)`
* In SQL, `TRUE OR NULL` results in `TRUE` but `FALSE OR NULL` results in `NULL`
  * So if a `deptno` was `40`, it would not be returned by the `NOT IN` because `NOT (deptno=10 OR deptno=50 OR deptno=null)` would equal `NOT (FALSE OR FALSE OR NULL)` which would equal `NOT (FALSE OR NULL)` which would equal `NOT (NULL)` which would equal `NULL`
* To handle `NULL` rows, use a correlated subquery + `NOT EXISTS`
  * It's called a "corrlated subquery" because rows from outer query are referenced inside the subquery

```sql
SELECT
  d.deptno
FROM dept AS d
WHERE NOT EXISTS (
  SELECT 1
  FROM emp AS e
  WHERE d.deptno = e.deptno
)
```

* `SELECT 1` will just return a `1` for every row in the result set

## Performing Joins when Using Aggregates

* Find the sum of the salaries for employees in department 10 along with the sum of their bonuses
  * Some employees have more than one bonus

```sql
SELECT
  deptno,
  SUM(sal) AS total_sal,
  SUM(bonus) AS total_bonus
FROM (
  SELECT
    e.empno,
    e.ename,
    e.sal,
    e.deptno,
    e.sal * (
      CASE WHEN eb.type = 1 THEN 0.1
           WHEN eb.type = 2 THEN 0.2
           ELSE 0.3
      END) AS bonus
  FROM emp AS e, emp_bonus AS eb
  WHERE e.empno = eb.empno
    AND e.deptno = 10
) AS x
GROUP BY deptno
```

* This SQL looks like it would sole the problem but it inaccurately sums salaries because it double-counts salaries for employees that have multiple bonuses
  * This is because the inner `SELECT` joins employees with the bonus table, so if an employee has multiple bonuses, the `e.sal` value will be duplicated over each matching row for the same employee (but different bonus)
* The robust solution is to sum the salaries in department 10 first, then to join that with the `emp` table, then join the `emp` table with the `emp_bonus` table and sum the bonuses

```sql
SELECT
  d.deptno,
  d.total_sal,
  SUM(
    e.sal * (
      CASE WHEN eb.type = 1 THEN 0.1
           WHEN eb.type = 2 THEN 0.2
           ELSE 0.3
      END) 
  ) AS total_bonus
FROM 
  emp AS e,
  emp_bonus AS eb,
  (
    SELECT
      deptno,
      SUM(sal) AS total_sal
    FROM emp
    WHERE deptno = 10
    GROUP BY deptno
  ) AS d
WHERE e.deptno = d.deptno
  AND e.empno = eb.empno
GROUP BY 
  d.deptno,
  d.total_sal
```

* The total employee salary is calculated in the `FROM` as (basically) another table (with a single row) and is joined with the join of employees and their bonuses (which are aggregated in the top-level `SELECT`)

## Returning Missing Data from Multiple Tables

* If you wanted to return the names of all departments and any matching employees (if they exist) a `LEFT OUTER JOIN` would be used like

```sql
SELECT
  d.deptno,
  d.dname,
  e.ename
FROM dept AS d
LEFT OUTER JOIN emp AS e
  ON (d.deptno=e.deptno)
```

* How to return this result set _and_ all rows for employees and their related department (even if the department doesn't exist)
* The solution is to use a `FULL OUTER JOIN` - this will return rows where there is a department without any employees (so `OPERATIONS` is the `dname` but `ename` is `NULL`) and employees without a department (so `YODA` is the `ename` value but `deptno` and `dname` are `NULL`)
* Conceptually it's like a `LEFT OUTER JOIN` and a `RIGHT OUTER JOIN` were `UNION`ed
 * `LEFT OUTER JOIN` is the departments with (or without) employees and `RIGHT OUTER JOIN` is employees with (or without) departments
