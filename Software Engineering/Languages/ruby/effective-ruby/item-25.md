# Item 25: Exit `ensure` Clauses By Flowing Off The End

* `tl;dr` - do not `return` in `ensure` clauses

```ruby
def do_not_do_this
  return 'foo'
ensure
  return 'bar'
end
```

* In non-exceptional situations, `bar` will be returned, so overrides the `return foo` in the body of the method
* In exceptional situations, `bar` will still be returned _and_ swallow any errors
  * Worse than `return`ing in a `rescue` because at least that will "only" catch `StandardError`s and things like `SyntaxError` or `NoMemoryError` won't be caught
  * `return`ing in `ensure` will swallow all those errors
* Similarly, don't `throw` inside `ensure`
* When iterating, calling `next` and `break` inside `ensure` will also silently discard exceptions
  * So never use `next` or `break` in `ensure`
  * It might make more sense to invert the relationship, placing the iteration inside the `begin`

```ruby
things.each do |thing
  begin
    # process thing
  ensure
    next
end
```