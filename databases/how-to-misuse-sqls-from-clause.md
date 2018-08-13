# [How To Misuse SQL's `FROM` Clause](http://www.onlamp.com/pub/a/onlamp/2004/09/30/from_clauses.html)

```sql
select distinct a.CUSTOMER_ID, a.CUSTOMER_NAME
from CUSTOMERS a,
     ORDERS    b
where a.ZIP_CODE in ...
  and b.ORDERED_DATE >= ...
  and b.CUSTOMER_ID = a.CUSTOMER_ID
order by a.CUSTOMER_NAME
```

* Notice how table `b` (i.e. `ORDERS`) is only used in the `WHERE` clause
* However, the fact that `ORDERS` is `JOIN`ed with `CUSTOMERS` means that a `DISTINCT` needs to be used in the `SELECT` because a customer could have ordered multiple times
* However, a `DISTINCT` shouldn't really need to be used since all the data is coming from `CUSTOMERS`
* Thus, instead of joining `CUSTOMERS` and `ORDERS`, a subquery should be used in the `WHERE`

## Correlated Subquery

* Defining characteristic of a correlated subquery is that it refers to at least one value from the current row of the outer query (`CUSTOMER_ID`)
* If no other filtering condition exists, we will have to evaluate subquery for each row we inspect during execution of outer query
  * This will probably be more expensive than `DISTINCT` + `JOIN`

```sql
select a.CUSTOMER_ID, a.CUSTOMER_NAME
from CUSTOMERS a
where a.ZIP_CODE in ...
  and exists (select NULL
              from ORDERS b
              where b.CUSTOMER_ID = a.CUSTOMER_ID
                and b.ORDERED_DATE >= ...)
order by a.CUSTOMER_NAME
```

## Uncorrelated Subquery

* Uncorrelated subquery only executes once and doesn't reference an outer query
* If rows returned are relatively small, engine will use an index
* If rows returned are large, engine will use a hash or merge join
  * This could prove more efficient than a correlated subquery since correlated subquery executes at least as many times as the number of rows returned

```sql
select a.CUSTOMER_ID, a.CUSTOMER_NAME
from CUSTOMERS a
where a.ZIP_CODE in ...
  and a.CUSTOMER_ID in (select b.CUSTOMER_ID
                        from ORDERS b
                        where b.ORDERED_DATE >= ...)
order by a.CUSTOMER_NAME
```
