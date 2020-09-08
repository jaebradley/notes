# Metadata Queries

## Listing Tables In A Schema

```sql
SELECT
  table_name
FROM information_schema.tables
WHERE table_schema = 'MY TABLE SCHEMA';
```

* PostgreSQL support something called the information schema, which is a set of views defined by the ISO SQL standard

## Listing A Table's Columns

```sql
SELECT
  column_name,
  data_type,
  ordinal_position
FROM information_schema.columns
WHERE table_schema = 'MY TABLE SCHEMA'
  AND table_name = 'MY TABLE NAME'
```

## Listing Indexed Columns For A Table

```sql
SELECT
  a.tablename,
  a.indexname,
  b.column_name
FROM pg_catalog.pg_indexas AS a,
     information_schema.columns AS b
WHERE a.schemaname = 'MY TABLE SCHEMA'
  AND a.tablename = b.table_name
```

* Note the cross-join / Cartesian product between `pg_indexes` and `columns`

## Listing Constraints On A Table

```sql
SELECT
  a.table_name,
  a.constraint_name,
  b.column_name,
  a.constraint_type
FROM information_schema.table_constraints AS a,
     information_schema.key_column_usabe AS b
WHERE a.table_name = 'SOME TABLE NAME'
  AND a.table_schema = 'SOME TABLE SCHEMA'
  AND a.table_name = b.table_name
  AND a.table_schema = b.table_schema
  AND a.constraint_name = b.constraint_name
```
