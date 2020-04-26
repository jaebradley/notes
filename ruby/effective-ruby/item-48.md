# Item 48: COnsider Memoizing Expensive Computations

```ruby
def current_user
  @current_user ||= User.find(some_user_id)
end
```

* `User.find` is only invoked once and the result is memoized in `@current_user` variable
* `@current_user ||= x` => `@current_user || @current_user = x`
  * If `@current_user` is `false` or `nil` (the two falsy values in Ruby), then `User.find` is called and the result is stored in `@current_user`
  * Else, `@current_user` will be returned instead of evaluating the right-hand side

* Can use `||=` to build cache hashmap

```ruby
def lookup(key)
  @cache ||= {}

  @cache[key] ||= begin
    # process key
  end
end
```

* If `lookup` is called more than once with the same key, it will only evaluate the `begin` block a single time (assuming the return of `begin` block is not falsy)
* Always consider side-effects associated with a memoized action - sometimes, there are necessary side-effects for certain actions that should run every single time that action is undertaken
* Returning mutable objects is less safe than returning immutable values (like `true` or `false`)
  * For example, is `current_user` is modified, then every time `current_user` is called, those responses will also reflect those modifications - instead, consider returning frozen objects from memoized methods