# [Ruby Memoization and Alternatives](https://robots.thoughtbot.com/ruby-memoization-and-alternatives)

* Memoization is a form of caching and comes with all the gotchas associated with caching (extra complexity and cache invalidation)
* Situations where benefits from caching are limited
  * Operations only done once - pay cost but don't get any of the benefits
  * Cheap operations done multiple times - pay cost but benefit of work trends toward zero unless there's a high volume of work being done
* Main situation where caching may be most beneficial - expensive operations that get called multiple times
* Most of the time, can convert memoized function to instance variable in constructor
  * However, this approach is *eager* in that all calculations are done on object instantiation
  * Might be benefit for deferring expensive calculations until they're needed
* Can use "laziness" to defer expensive calculations until they're needed
  * Instead of doing work in constructor, do work in method
  * In this method, perform expensive calculation and before returning the result, set the value as an instance variable
  * This instance variable (assuming that it doesn't need to be recalculated) can be used when the method is called again

## Separate caching and calculation

* Separation of concerns that makes it easy to re-run calculations

```ruby
class Dashboard
  def stats
    @stats ||= do_expensive_work
  end

  private

  def do_expensive_work
    # do expensive work
  end
end

class Dashboard
  attr_reader :stats

  def initialize
    @stats = do_expensive_work
  end

  private

  def do_expensive_work
    # do expensive work
  end
end
```
