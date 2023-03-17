# [Performance gains from using foreign keys](https://www.depesz.com/2010/10/06/performance-gains-from-using-foreign-keys/)

* A `products` table and a `sales` table
* `sales` table is polymorphic - it has a `sold_object_type` and a `sold_object_id`
  * `types` could be `products`, `services`, etc. so depending on the type the `sold_object_id` refers to different tables
* Because there were no constraints on `sold_object_id` the query to identify how many different products were sold in a timeframe needed to join to `products`

```sql
SELECT
  COUNT(DISTINCT p.id)
FROM
  sales s
  JOIN products p on s.sold_object_id = p.id
WHERE
  s.sold_object_type = 'product'
  AND sold_whne BETWEEN $start AND $end
```

* join slows down query especially several million products and many sales
* A foreign key would help because the reason for the join is because there are no guarantees that every row matches a product
  * Every row that has a `sold_object_type = 'product'` implies that there is a row in the `products` table that has the `sold_object_id` for that row
  * The existence of the foreign key makes this guarantee
  * Should also be duplicated in application code to avoid unnecessary round-trips, for example)
