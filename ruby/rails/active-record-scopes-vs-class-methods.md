# [Active Record Scopes vs. Class Methods](http://blog.plataformatec.com.br/2013/02/active-record-scopes-vs-class-methods/)

* Internally, Active Record converts a scope into a class method

```ruby
def self.scope(name, body)
  singleton_class.send(:define_method, name, &body)
end
```

```ruby
scope :published, where(status: 'published')

# gets converted into

def self.published
  where(status: 'published')
end
```

* Scopes are always chainable - they will always return an Active Record Relationj
  * If an input value is blank, can just add a presence condition like `scope :by_status, -> status { where(status: status) if status.present? }`
  * If doing the equivalent for a class method, would need to do

  ```ruby
def self.by_status(status)
  if status.present?
    where(status: status)
  else
    all
  end
end
```

* Never return `nil` from a class method that should work like a scope
* Use scopes when the logic is limited like simple where/order clauses and class methods when there is more complexity
