# [Waiting for PostgreSQL 11 - Fast ALTER TABLE ADD COLUMN with a non-NULL default](https://www.depesz.com/2018/04/04/waiting-for-postgresql-11-fast-alter-table-add-column-with-a-non-null-default/)

* `ALTER TABLE x ADD COLUMN y text` - very fast as it locks table, adds information about new column
* `ALTER TABLE x ADD COLUMN y DEFAULT 'some default'` - very slow since it rewrote the entire table adding column to each row and adding default value
