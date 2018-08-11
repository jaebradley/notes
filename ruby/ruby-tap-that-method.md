# [Ruby: Tap that method](https://medium.com/aviabird/ruby-tap-that-method-90c8a801fd6a)

```ruby
# with tap
user = User.new.tap do |u|
  u.username = 'kartik'
  u.save!
end

# without tap
user = User.new
user.username = 'kartik'
user.save!
```

* `tap` allows you to do something with an object inside of a block and have that block return the object itself
* Idea is that `tap` increases readability by putting everything into a block
* Debugging chained method

```ruby
# you can turn
(1..10).to_a.select { |x| x % 2 == 0 }.map({ |x| x * x} )

# into
(1..10).tap { |x| puts "original: #{x.inspect}" }.to_a.
  tap { |x| puts "array: #{x.inspect}" }.
  select { |x| x%2 == 0 }.
  tap    { |x| puts "evens: #{x.inspect}" }.
  map    { |x| x*x }.
  tap    { |x| puts "squares: #{x.inspect}" }
```
