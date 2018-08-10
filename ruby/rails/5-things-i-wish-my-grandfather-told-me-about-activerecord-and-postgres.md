# [5 Things I Wish My Grandfather Told Me About ActiveRecord And Postgres](https://medium.com/carwow-product-engineering/5-things-i-wish-my-grandfather-told-me-about-activerecord-and-postgres-93416faa09e7)

* In an `explain` plan, `width` is measured in `bytes`
* Use `pg_statement` timeouts
  * Configure this in `database.yml` using an `ENV` variable
  * This can be overridden in background jobs using `ENV` variable
* Lookout for `COUNT(*)` - it's going to (obviously) have to scan the full result set
  * Could use Rails' built-in `counter_cache` option for `has_many` and `belongs_to` relations
