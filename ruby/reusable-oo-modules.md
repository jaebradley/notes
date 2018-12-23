# [Reusable OO Modules](https://robots.thoughtbot.com/reusable-oo-modules)

* Imagine API adapter for fetching directors from movie facts service
* Comes in two types - read from HTTP and read from cache
  * Shared logic between both types in shared in a base class
* Imagine API has pagination
  * So now need to make multiple requests and combine results together
  * However, there are now `4` ways to get data
    * `DepaginatedHTTPClient`
    * `PaginatedHTTPClient`
    * `DepaginatedCacheClient`
    * `PaginatedCacheClient`
    * Because of the principle of "separate things that change from things that stay the same" we need `4` different clients for each combination
    * Could get clever and create two intermediary base classes (`HTTPClient` and `CacheClient`) which their `Depaginated` versions extend from
      * However, there's still probably some duplicated depagination logic between those two sibling classes

## Using Modules / Mixins for Multiple Inheritance

* Imagine there's a `Depaginatable` module that makes multiple calls to `fetch_data` and then combines all the data together

```ruby
module Depaginatable
  def fetch_depaginated_data
    # Call fetch_data multiple times
  end
end
```

* Can use this module in the respective clients

```ruby
module MovieFacts
  class HttpClient < ClientBase
    include Depaginatable

    private

    def fetch_data
      # make HTTP request
      # cache response
    end
  end
end

module MovieFacts
  class CacheClient < ClientBase
    include Depaginatable

    private

    def fetch_data
      # read data from cache
    end
  end
end
```

* Multiple inheritance, like simple inheritance, suffers from encapsulation problems
  * While code duplication has been limited, logic is still shared / duplicated across clients
  * For example, `HTTPClient` knows how to fetch data, depaginate it, and convert it into Director instances
    * Think about this from the viewpoint where a sub-class is all its ancestors combined
    * Thus, there isn't a clear demarcation of responsibilities - each ancestor has access to private methods and instance state of combined object
      * This means that it's easy to refactor functionality of one of the ancestors and breaks functionality for one of the other ancestors
