# [Building Reusable Object-Oriented Systems: Composition](https://robots.thoughtbot.com/reusable-oo-composition)

* Instead of building objects that does everything through inheritance, create several smaller objects that do one thing and then combine them via composition
* `Client` object creates `Director` instances based on data from the API
  * Unconcerned with how to make requests or depaginate them - relies on passed in objects to do this

```ruby
module MovieFacts
  class Client
    def initialize(driver, depaginator)
      @driver = driver
      @depaginator = depaginator
    end

    def directors
      fetch_data("/directors").map { |director| Director.new(director) }
    end

    def director(name)
      Director.new(fetch_data("/directors/#{name}"))
    end

    private

    def fetch_data(path)
      @depaginator.depaginate @driver.fetch_data(path)
    end
  end
end
```

* Multiple `Driver`s that have different logic for fetching data (from cache or from the API) though they all implement the same interface (`fetch_data`)

```ruby
module MovieFacts
  class HttpDriver
    def fetch_data
      # fetch data over HTTP
      # cache results
    end
  end
end

module MovieFacts
  class CacheDriver
    def fetch_data
      # read data from cache
    end
  end
end
```

* `Depaginator` takes paginated data and combines it into a single array
  * One depaginator is a no-op since we don't necessarily need to depaginate
  * Implementations have the same interface

```ruby
module MovieFacts
  class Depaginator
    def depaginate(data)
      # depaginate the data
    end
  end
end

module MovieFacts
  class NoopDepaginator
    def depaginate(data)
      data # just return the data without de-paginating it
    end
  end
end
```

* Clear to see how you can combine these objects to create the object / behavior that you want
* Also able to extend the behavior
  * Need a different data source? Add a different driver
  * Want to depaginate in a different way? Like using a lazy stream? Create a different depaginator
  * Need to do different processing? Pass in another object to the `Client`
  * Need to refactor one of the objects? Only dependency is the public interface
