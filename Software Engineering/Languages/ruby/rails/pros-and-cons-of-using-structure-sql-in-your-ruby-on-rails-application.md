# [Pros and Cons of Using structure.sql in Your Ruby on Rails Application](https://blog.appsignal.com/2020/01/15/the-pros-and-cons-of-using-structure-sql-in-your-ruby-on-rails-application.html)

* `schema.rb` is great for relatively basic applications and use cases
  * Created by inspecting database and expressing structure using Ruby
  * It is database-agnostic so the syntax and structure will remain largely the same
* `schema.rb` can be problematic when there are many migration files
  * Running them all in sequence (to spin up a production system, for example) might take too long
  * Or some migrations contain code that was meant to be executed on an older version of the database but cannot be executed on a newer version
* `structure.sql` is an exact copy of a database structure - it's easy to spin up a new database using `rails db:setup`
  * Allows ability to use views, materialized views, functions, triggers, sequences, shared procedures, check constraints, etc.
* Once you are done working on a branch that contains migrations, make sure to run `rails db:rollback STEP=n` where `n` is the number of migrations on the branch - this should revert database to original state
* When working on a new branch, make sure to pull a new `structure.sql` file from `master` before creating any new migrations
