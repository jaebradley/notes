# [Eventual Consistency For Streaming](https://materialize.io/eventual-consistency-isnt-for-streaming/)

* Eventual consistency is most often invoked for key/value stores like a map from people to addresses
  * This is because the requirement to maintain consistency is if people stop updating a specific key
  * The rest of the world can keep reading out addresses - an eventually consistent system is just obligated to eventually update your address
  * Generally speaking, eventual consistency is a workable definition of consistency for key/value stores because most operations do not conflict and one can reasonably expect to wait out any inconsistency

## Streaming Computations

* Author focuses on a particular type of streaming computation called incremental view maintenance
  * Incremental view maintenance is when you've defined a view (i.e. a name bound to a query) and want to see the output answers change as the input data change

## Eventual Consistency When Counting A Dataset

```sql
select count(*) from data
```

* One approach to defining eventual consistency is that if the input (`data`) stops changing entirely, the system will eventually update the correct count of records in `data`
## Eventual Consistency When Getting Max Values

```sql
select data.key
from data
where data.value in (select max(data.value) from data)
```

* If input stream is allowed to change, an eventually consistent system _may_ not see some keys (i.e. the keys associated with max values)
  * This might occur if the inputs from `data` arrive later than for `max(data.value)`
  * Not unreasonable since it's easier to maintain `max` than to maintain entire collection (i.e. `data`)
* So `max(data.value)` might be, say, `2000` but if `data` is lagging so that `2000` hasn't been inserted into `data` yet (even though `max(data.value)` _has_ been updated) then the query won't return anything
