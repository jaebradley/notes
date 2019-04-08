# [PostgreSQL Concurrency: Isolation and Locking](https://tapoueh.org/blog/2018/07/postgresql-concurrency-isolation-and-locking/)

* SQL standard defines four levels of transaction isolation that are described by the following anomalies
  * Dirty read - a transaction reads data from a concurrent uncommitted transaction
  * Nonrepeatable read - a transaction re-reads data that it's already read and finds that data has been modified after a concurrent transaction was committed
  * Phantom read - a transaction re-executes a query satisfying a search condition and finds the set of returned rows is modified due to recently committed transaction
  * Serialization anomaly - result of successfully committing a group of transactions is different from all possible orderings of running the transactions one at a time
* "read committed", which Postgres implements by default disallows dirty reads
* "repeatable read" disallows dirty reads, nonrepeatable reads, and phantom reads
* "serializable" disallows all anomalies

## Concurrent updates and isolation

```sql
update tweet.message
  set rts = rts + 1
where messageid = 1;
```

* To simulate concurrency, execute above update in uncommitted transaction and then open up another psql instance to execute the same in another uncommitted transaction

```sql
begin;

   update tweet.message
      set rts = rts + 1
    where messageid = 1;
returning messageid, rts;
```

```bash
# This is returned
 messageid │ rts
═══════════╪═════
         1 │   2
(1 row)
```

* The transaction is open ("idle in transaction") and is waiting for a commit or rollback
* Opening a second psql instance and executing the same query won't return the previous result
  * This is because the first transaction has not completed and the row is locked
  * Until transaction is done no concurrent activity can take place on row
* When first transaction is committed
  * If second transaction has isolation level of "read committed" (which Postgres has by default), transaction will return `rts` value of 3
  * If second transaction has isolation level of "repeatable read", this will error (`ERROR:  could not serialize access due to concurrent update`) and `ROLLBACK`
    * Which can be defined by `start transaction isolation level repeatable read;`
