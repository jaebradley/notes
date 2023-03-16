# Item 27: Prefer `throw` To `raise` For Jumping Out Of Scope

* One scenario where exceptions are leaned on for control flow is in the case of nested loops where `break` will only terminate in one of the loops
* However, better approach is to use `catch` and `throw`

```ruby
match = catch(:jump) do
  values.each do |value|
    moar_values.each do |moar_value|
      if moar_value == value
        throw(:jump, moar_value)
      end
    end
  end
end
```

* `catch` and `throw` work by labeling a section of the code with the `catch` keyword and then using `throw` to jump back to that label
  * When using `throw` to jump across stack frames, all intermediate `ensure` clauses will run
* `catch` takes a symbol and a block to execute
  * If `throw` is used in that block with the same label, then `catch` will terminate the block and return the value given to `throw`
  * Don't have to pass a value to `throw` - if no value is passed, it will be `nil`
  * If `throw` isn't called during execution of block then `catch` will return last value of block