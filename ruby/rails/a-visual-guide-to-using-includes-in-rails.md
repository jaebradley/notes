# [A Visual Guide To Using `includes` in Rails](https://engineering.gusto.com/a-visual-guide-to-using-includes-in-rails/)

* `includes` will either use `preload` or `eager_load`
* `preload` will be used if no `where` or `order` is specified
  * `preload` will execute two queries
    * The first query will select all records from the leading model
    * The second query will load records from the associated model based on the foreign keys from the first query's results
  * `Employee.preload(:forms)` will result in
    * `SELECT * FROM employees`
    * `SELECT * FROM forms WHERE forms.employee_id IN (ALL_EMPLOYEE_IDS)`
* `eager_load` will be used when a `where` or `order` is specified
  * Produces one query that uses a left outer join to build an intermediary table
* If things were reversed like `Form.includes(:employees)` the queries are
  * `SELECT * FROM forms`
  * `SELECT * FROM employees WHERE employee_id IN (ALL_FORM_EMPLOYEE_IDS)`
* Gusto's not-so-scientific-trialing showed that preloading seemed to be way superior to `N+1` but eager load seemed slower than `N+1`?

