# Chapter 2: Choose the Right Control Structure

* Use modifier forms when appropriate

```ruby
# Instead of this...
if x
  do y
end

# Prefer this...
do y if x
```

* This will work for `if` / `unless` and `while` / `until`
* Ruby actually uses `each` when calling a `for` loop - mind as well just use `each` every time?
* Only `false` and `nil` are evaluated as falsy - everything else is `true` (even `0`)
* If evaluating a boolean expression and really looking for `nil`, convert to look for `nil` directly using `nil?` so as to eliminate any possibility of evaluating `false`
  * Same concept with `||=` - if variable is falsy, variable will get reset, so don't use `||=` with booleans
