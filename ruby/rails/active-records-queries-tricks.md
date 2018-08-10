# [`ActiveRecord's` Queries Tricks](https://medium.com/rubyinside/active-records-queries-tricks-2546181a98dd)

## `Join` Query with Condition on Associated Table

* `User`s with `Profile`
  * Querying active `User` `Profile`s might look like

```ruby
# User model
scope :activated, -> {
  joins(:profile).where(profiles: { activated: true })
}
```

* Implementation details about `Profile` is "leaking" (`User` needs to know about the `activated` field)

```ruby
# Profile model

scope :activated, -> { where(activated: true) }

# User model

scope :activated, -> { joins(:profile).merge(Profile.activated) }
```

## Unnecessarily using `pluck`

```ruby
Post.where(user_id: User.created_last_month.pluck(:id))
```

* This query will execute two queries
  * One to fetch ids of `User`s
  * Another to query `Post`s
* Can do all of this with one query: `Post.where(user_id: User.created_last_month)`

## `where.not` and `booleans`

```ruby
User.where.not(tall: true)
```

* Problem with this query is that unlike `WHERE IS NOT true` (which will return `null` values), `where.not(true)` is equivalent as `where(false)`
  * In order to capture `NULL` values, need to do `User.where(tall: [false, nil])`
