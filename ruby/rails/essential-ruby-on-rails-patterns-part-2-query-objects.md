# [Essential RubyOnRails Patterns - Part 2: Query Objects](https://medium.com/selleo/essential-rubyonrails-patterns-part-2-query-objects-4b253f4f4539)

* As a rule of thumb, if scope interacts with more than one column and/or joins in other tables, it should potentially be moved to a query object
  * Or if a chain of scopes is introduced
* One nice side-effect is that the amount / number of scopes defined in the models is kept to a minimum
* Always suffix query object name with `Query` (or come up with some consistent naming schema)
  * Can also use pluralized name of model that a query is designed to work with
* Accept a `Relation` as first argument
  * Helps make query objects chainable
  * Necessary if query objects combine scopes
* Group query objects in namespaces
  * Use name of model those queries deal with
  * Store in `app/queries`

```rails
module Users
  class WithRecentlyCreatedProjectQuery
    DEFAULT_RANGE = 2.days

    def self.call(relation = User.all, time_range: DEFAULT_RANGE)
      relation.
        joins(:projects).
        where('projects.created_at > ?', time_range.ago).
        distinct
    end
  end
end
```

