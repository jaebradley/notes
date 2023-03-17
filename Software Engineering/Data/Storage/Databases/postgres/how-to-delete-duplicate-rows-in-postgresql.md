# [How To Delete Duplicate Rows in PostgreSQL](https://medium.com/@josemarcialportilla/review-of-sql-joins-ac5463dc71c9)

## Delete using cross-join

* Imagine simple table with two columns - `id` and `fruit`
  * `fruit` has duplicate values (many `apple`s, and `orange`s, for example)
* Find duplicate rows by usying `COUNT(fruit) > 1`
* Delete duplicate rows using `DELETE USING`

```sql
DELETE
FROM basket AS a
  USING basket AS b
WHERE a.id < b.id
  AND a.fruit = b.fruit
```

* `FROM basket AS a USING basket AS b` does a cross-join (cartesian product of every row against itself)
* `a.fruit = b.fruit` means to find all cells where the fruit values match
* `a.id < b.id` means to find all cells where the ids are different

## Delete using subquery

* Use the `row_number` window function to do a count of `fruit` values (ordering by `id`)
* Get `id`s where the row number value is greater than `1`
* Delete these `id`s from the table

```sql
DELETE
FROM basket
WHERE id IN (
  SELECT id
  FROM (
    SELECT
      id,
      ROW_NUMBER() OVER(
        PARTITION BY fruit
        ORDER BY id
      ) AS row_number
      FROM basket
    ) AS t
  WHERE t.row_number > 1
)
```

