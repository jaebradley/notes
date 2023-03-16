# [Don't Inline Rescue in Ruby](https://robots.thoughtbot.com/don-t-inline-rescue-in-ruby)

* `do_something rescue SomeException` - does **not** mean "execute `do_something` and then if `SomeException` is raised, carry on as if nothing happened"
* `do_something rescue nil` translates to
  * call `do_something`
  * if **any error** is raised return `nil`
  * else return result of `do_something`
  * shorthand for this

```ruby
begin
  do_something
rescue
  nil
end
```

* Following example will **always** return `nil` despite looking like that it should only return `nil` a small fraction of the time
* This is because of the typo (`something_is_wrong?` vs. `someting_is_wrong?`)
  * A `NoMethodError` is `raise`d, and is caught by the `begin/rescue` (unqualified `rescue` will `rescue` any exception inheriting from `StandardError`)

```ruby
def someting_is_wrong?
  rand < 0.01 # Good 99% of the time
end

def do_something
  if something_is_wrong?
    raise MyIgnorableException
  else
    "foo"
  end
end

begin
  do_something
rescue
  nil
end
```

* Should **always** qualify `rescue`s (i.e. don't just `rescue` or even `rescue StandardError`)
* Don't use inline `rescue`s as they are unqualified `rescue`s
  * Also could seem to imply that it `rescue`s a specific type of error
