# [Transactional Fixtures in Rails](https://anti-pattern.com/transactional-fixtures-in-rails)

* When using Rails, `DatabaseCleaner` is unnecessary because of transactional fixtures
* Transactional fixtures wrap each test in a database transaction and start a rollback at the end
  * Database ends up back in state before test
* Motivation for `DatabaseCleaner` was "to have an easy way to turn on what Rails calls `transactional_fixtures` in non-Rails `ActiveRecord` projects
