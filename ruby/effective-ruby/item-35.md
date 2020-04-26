# Item 35: Think Carefully Before Using Module Prepending

* While `include` inserts a list of modules between receiver and its superclass, `prepend` inserts them **before** the receiver

```ruby
module A
  def who_am_i?
    "A#who_am_i?"
  end
end

module B
  def who_am_i?
    "B#who_am_i?"
  end
end

class C
  prepend(A)
  prepend(B)

  def who_am_i?
    "C#who_am_i?"
  end
end

C.ancestors
# [B, A, C, Object, Kernel, BasicObject]

C.new.who_am_i?
# B#who_am_i?
```

* Prepending `A` and `B` modules to `C` will make them show up _before_ `C` in list of ancestors
* Calling overridden method will trigger implementation in `B` module before definition of `C#who_am_i?`
* Can no longer override a module's method simply by defining a version of that method in the class you're working with
  * Only way to call the class method is if the module's method calls `super`