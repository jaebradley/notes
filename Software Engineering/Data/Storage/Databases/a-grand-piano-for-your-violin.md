# [A Grand Piano For Your Violin](https://robots.thoughtbot.com/a-grand-piano-for-your-violin)

* Index every column used in a `WHERE` clause
* Index composite keys on join models
* State columns should be indexed
  * Comments that are `draft`, `submitted`, `published`, `unpublished` all have a state
  * Similarly `boolean` columns should be indexed
* Datetime columns should be indexed
  * Most likely performing queries that order by `created_at` (or `published_at` / `updated_at`)
* Columns used in validations
  * If you have column with uniqueness constraint - every time save record of that class (regardless of whether column has changed) `ActiveRecord` is going to run a query to try to find other rows which have the same data in that column
  * Much faster to do this on indexed columns than non-indexed columns
  * Example is `user.email` column, where the email has to be unique
