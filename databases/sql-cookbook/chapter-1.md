# Chapter 1

## Referencing an Aliased Column in the WHERE Clause

```sql
SELECT
  sal AS salary,
  comm AS commission
FROM emp
WHERE salary < 5000
```

* The above query will fail because the `salary` alias cannot be referenced in the `WHERE` clause
* Need to wrap query in an inline view to reference the aliased columns

```sql
SELECT *
FROM (
  SELECT
    sal AS salary,
    comm AS commission
  FROM emp
) AS x
WHERE salary < 5000
```

* This is because the `WHERE` clause is evaluated before the `SELECT` and thus `salary` and `commission` do not exist yet, so in the initial query, these aliases are not applied until _after_ the `WHERE` clause has completed processing
* The `FROM` clause is evaluated before the `WHERE` so by placing the original query inside the `FROM`, the results of the query are generated before the outside `WHERE`, and the outside `WHERE` evaluates the alias names

## Finding Null Values

* Use `IS NULL`
* `NULL` is never equal / not equal to anything, not even itself, therefore you cannot use `=` or `!=` for testing whether a column is `NULL`

## Transforming Nulls into Real Values

* Use `coalesce` like `coalesce(some_column, 0)` to replace `NULL` values with `0`
* Can technically use `CASE` like `CASE WHEN some_column IS NOT NULL THEN some_column ELSE 0 END` but `coalesce` is more succinct
