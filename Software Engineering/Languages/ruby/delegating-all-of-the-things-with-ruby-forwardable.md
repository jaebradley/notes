# [Delegating All The Things With Ruby Forwardable](http://vaidehijoshi.github.io/blog/2015/03/31/delegating-all-of-the-things-with-ruby-forwardable/)

* `extend Forwardable`
* `def_delegators` - first arg is thing to delegate *to*, remaining args are names of delegated methods
  * `def_delegators :foo, :bar, :baz` - this delegates the `bar` and `baz` methods of this class to the `foo` object
* `def_delegator` - first arg is thing to delegate *to* - next arg is method to delegate to, final arg is the new name for that method for this class
  * `def_delegator :foo, :bar, :baz` - this delegates the `baz` method of this class to the `foo` object's `bar` method

```ruby
# While underlying @books is an array, can make a more readable API using delegators
# ReadingList.remove_book vs. ReadingList.book.shift

class ReadingList
  extend Forwardable

  def_delegator :@book, :shift, :remove_book
  def_delegator :@book, :push, :add_book

  def initialize
    @books = []
  end
end
```
