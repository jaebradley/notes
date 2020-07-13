# Chapter 2

## Sorting By Multiple Fields

* You can order by using the numeric position of a column in the `SELECT` list
* Generally permitted to order by a column not in the `SELECT` list but to do so you must explicitly name the column
* Using `GROUP BY` or `DISTINCT` means that you cannot order by columns _not_ in the `SELECT` list

## Dealing with Nulls when Sorting

* Use a `CASE` expression to flag if a value is `NULL`
  * Use two values - one when the value is `NULL` and one when it's not
  * Add this flag to the `ORDER BY` - this way, can control if `NULL`s are sorted first or last without interfering with non-`NULL` values

```sql
SELECT
  ename,
  sal,
  comm
FROM (
  SELECT
    ename,
    sal,
    comm
    CASE
      WHEN comm IS NULL
      THEN 0
      ELSE
    END AS is_null
  FROM emp
) AS x
ORDER BY is_null DESC, comm
```

* The SQL above would sort `comm` values ascending, with any `NULL` `comm` values last

## Sorting on a Data Dependent Key

* Example is if you want to sort by `comm` column if `job` value is `SALESMAN`, otherwise sort by `sal` column value
* Use `CASE` expression in `ORDER BY`

```sql
SELECT
  ename,
  sal,
  job,
  comm
FROM emp
ORDER BY
  CASE
    WHEN job = 'SALESMAN'
    THEN comm
    ELSE sal
  END
```

* Can also move this `CASE` expression to the `SELECT` and then sort by the result in the `ORDER BY`
