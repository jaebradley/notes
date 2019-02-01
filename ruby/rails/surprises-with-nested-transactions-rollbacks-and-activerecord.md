# [Surprises with Nested Transactions, Rollbacks, and ActiveRecord](https://pragtob.wordpress.com/2017/12/12/surprises-with-nested-transactions-rollbacks-and-activerecord/)

* How many users does this query create?

```ruby
User.transaction do
  User.create(name: 'foo')
  User.transaction do
    User.create(name: 'bar')
    raise ActiveRecord::Rollback
  end
end
```

* It creates two users
* This is because the inner transaction catches the `ActiveRecord::Rollback` exception and then the outer transaction commits everything
* If an exception and not caught by inner transaction then everything gets rolled back as expected
* `requires_new` will emulate nested transaction using savepoints
  * So in example above, if inner transaction had `required_new` set to `true` then only one `User` would be created
