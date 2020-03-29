# Item 4: Be Aware That Constants Are Mutable

* Constants are like global variables than unchanging values
* Always `freeze` constants to prevent them from being mutated
  * If a collection contains elements, freezing that collection is not sufficient as the elements within the collection are _still_ mutable

```ruby
# Instead of

NETWORKS = ['some', 'network'].freeze

# Need to do

NETWORKS = ['some', 'network'].map!(&:freeze).freeze
```

* Can still assign a _new_ value to an existing constant (even if the current value _is_ frozen)

```ruby
TIMEOUT = 5
TIMEOUT += 5

# TIMEOUT is 10
```

* To prevent Ruby from assigning new values to existing constants, freeze the class or module they're defined in
* Structure code so that all constants are defined in their own module

```ruby
module Defaults
  TIMEOUT = 5
end

Defaults.freeze
```