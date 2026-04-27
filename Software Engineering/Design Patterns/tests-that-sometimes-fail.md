# [Tests That Sometimes Fail](https://samsaffron.com/archive/2019/05/15/tests-that-sometimes-fail)

## Hard-Coded Ids

* Don't make assumptions about a record's `id` starting at `1` because while test frameworks rollback database transaction after test runs, the rollback will not reset sequences (which are used by Postgres to set the id for new records)

## Making Bad Assumptions About DB Ordering

* Inserting two values in a certain order does not mean that `select`ing from the table `limit 1` will return the first value inserted into the table - use `order by X` to guarantee that the first value is returned

## Leaky Global State

* Amending global state and not resetting back to original state can cause flakiness

```ruby
class Frog
   cattr_accessor :total_jumps
   attr_accessor :jumps

   def jump
     Frog.total_jumps = (Frog.total_jumps || 0) + 1
     self.jumps = (self.jumps || 0) + 1
   end
end

# works fine as long as this is the first test
def test_global_tracking
   assert(Frog.total_jumps.nil?)
end

def test_jumpy
   frog = Frog.new
   frog.jump
   assert(frog.jumps == 1)
end
```

* Testing `test_jumpy` first and then `test_global_tracking` fails - other way works
  * Since `Frog.new` + `jump` method sets the `jumps` attribute to no longer be `nil`
* Author's company runs tests in random order (which helps pick up order-dependent tests)

## Bad Assumptions About The Environment

* Author's company had process for downloading images from a remote source
* Download only occurred if there was ample space on the machine
* This means that if the test suite was ran on a machine strained for disk space, the tests would start failing

## Resource Leaks

* If you forget to properly clean up a few event handlers, over thousands of tests this can lead to leaks that make fast tests gradually become very slow or break inconsistently
* Tests consistently work on production CI but fail on resource-strained Travis CI environment

## Add Diagnostic Code To Debug Flaky Tests

* Add logging or some other tracing to flaky tests that haven't been reproduced locally so that there's a fighting chacne of figuring out what state caused it
