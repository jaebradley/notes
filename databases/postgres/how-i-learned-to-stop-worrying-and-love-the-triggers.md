# [How I Learned to Stop Worrying and Love the Triggers](https://www.depesz.com/2012/11/14/how-i-learned-to-stop-worrying-and-love-the-triggers/)

* Trigger is a function that gets automatically called in case of a database event (`INSERT`, `UPDATE`, `DELETE`, TRUNCATE`)
  * Triggers can be called before event or after event
  * Triggers can be called on each affected row or per statement
  * For each row triggers have special variables that contain content of row being modified so that they can be used in function while for each statement triggers have no context about the rows that were modified
* Before triggers are called before internal row representation is updated while after triggers are called, well, after
  * Before triggers should be used to modify new values inserted or updated to the table
    * Don't add an after insert trigger that will `UPDATE` recently inserted record - it will be slower. Instead, in before trigger, modify row data before it is stored in table.
  * After triggers should be used when modifications to _other_ tables needs to occur after modifications to existing table succeeds

## Before Triggers

* Example using a `users` table where the `fullname` values should be normalized
  * Each trigger calls a function, so the logic is in the function and NOT in the trigger so the function can be shared between multiple triggers, on multiple tables
  * Function should not accept any arguments and return the `TRIGGER` pseudotype
  * For the `FOR EACH ROW` triggers, the `NEW` and `OLD` record variables are available

```plpgsql
CREATE FUNCTION normalize_fullname() RETURNS TRIGGER AS
BEGIN
  NEW.fullname := initcap(NEW.fullname);
  RETURN NEW;
END;
LANGUAGE plpgsql;
```

* First line defines function with it's name, it's arguments (or lack thereof) and the return value of the pseudotype `TRIGGER`
* Second line defines start of function body that must be contained inside a `BEGIN` / `END` block
* `NEW` is record that contains data for new row - so after the `NEW.fullname` column value is processed, it is assigned to `NEW.fullname` to ensure new value is actually updated
* Function must return something - the returned value is used to insert/update data

```psql
CREATE TRIGGER normalize_fullname_trg
  BEFORE INSERT OR UPDATE
  ON users
  FOR EACH ROW
  EXECUTE PROCEDURE normalize_fullname();
```

* Returning `NULL` will _not_ perform modification and will also not raise any type of exception or error
* Can also use triggers to do validations by calling `RAISE EXCEPTION` to invalidate transaction but really this logic should be contained in `CHECK` contraints and not triggers

## After Triggers

* A `privileges` table where a row exists for every user with a basic `none` privilege

```psql
CREATE FUNCTION make_default_privileges() RETURNS TRIGGER AS
BEGIN
  INSERT INTO privileges(user_id, privileges) VALUES (NEW.id, ARRAY['none'[::text[]);
  RETURN NEW;
END;
LANGUAGE plpgsql;

CREATE TRIGGER make_default_privileges_trg
  AFTER INSERT
  ON users
  FOR EACH ROW
  EXECUTE PROCEDURE make_default_privileges();
```

* Can use `BEFORE` trigger but shouldn't
  * If a foreign key exists on `privileges` that references `users`, this foreign key will complain because the row in `users` does not exist yet
  * Many triggers can exist on a single row - some triggers may reject row, so don't do more work until dependent data is guaranteed to have been updated
* In `AFTER` trigger, returned value does _not_ matter
* If a modification fails, will effects of trigger also be rolled back?
  * Even if transactions aren't defined explicitly, every query sent to Postgres is wrapped in a transaction
  * So command and it's triggers are always within the same transaction
  * So if trigger raises exception, it will also remove changes that command applied
  * Also means that if trigger raises exception, effects of all triggers ran by command will also be rolled back
* Application-level triggers via ORM can lead to two related modifications that are executed in separate transactions and can lead to accidentally rolling back one but not the other

## Using triggers to ensure data modification

* `users` table that has `created_at` and `updated_at` columns
  * `created_at` should only be set on `INSERT` and set to `now`
  * `updated_at` should only be set on `UPDATE` and set to `now` at that point in time, even if it was forgotten in `UPDATE` statement
* Can use two separate triggers, but will use a single trigger to show how to use `TG_OP` variable

```psql
CREATE FUNCTION sanitize_dates() RETURNS TRIGGER AS
BEGIN
  IF TG_OP = 'INSERT' THEN
    NEW.created_at := now();
    NEW.updated_at := NULL;
  ELSEIF TG_OP = 'UPDATE' THEN
    NEW.created_at := OLD.created_at;
    NEW.updated_at := now();
  END IF;
  RETURN NEW;
END
LANGUAGE plpgsql;

CREATE TRIGGER sanitize_dates_trg
  BEFORE INSERT OR UPDATE
  ON users
  FOR EACH ROW
  EXECUTE PROCEDURE sanitize_dates();
```

## How to take advantage of statement triggers

* Store total count of all rows in a table
  * So instead of doing a select count(*) there is some rollup table that tracks counts for each table (or something)
  * Using for each row triggers could be problematic if inserting or deleting many many rows because `counts` table would get updated many many times
  * Smarter approach is to have a counter in memory which gets incremented by `ROW` triggers which eliminates table write overhead
  * Then use `AFTER STATEMENT` trigger to actually update table
  * Can store in-memory counter in a system setting
* Three triggers are necessary 
  * `BEFORE` statement trigger to create in-memory counter and set it to `0`
  * `ROW` trigger to update counter on each row
  * `AFTER` statement trigger to update count diff in table

