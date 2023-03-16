# [Modeling a Paginated API as a Lazy Stream](https://robots.thoughtbot.com/modeling-a-paginated-api-as-a-lazy-stream)

* Suppose client for a Names API looks something like this and is paginated

```ruby
module Names
  class Client
    Name = Struct.new(:id, :name, :births)
    BASE_URL = "http://name-service.com"

    def all_names
      fetch_data("/users").
        map { |data| convert_to_name(data) }
    end

    private

    def fetch_data(path)
      HTTParty.get(BASE_URL + path).
    end

    def convert_to_name(data)
      Name.new(data["id"], data["name"], data["births"])
    end
  end
end
```

* Would like to deal with data as a single list
  * The fact that the names are paginated is an implementation detail of the API
  * This list should be as lazy as possible so minimum number of API calls are made

## Using `Enumerator`

* Makes requests
* When requests are non-`2xx`, stop (assumption is that when a page count is too high, the response code will be a `404`)
* Else, yield out item one at a time
* After all results have been yielded out, increment page count and make a request for next page
  * Repeat same process

```ruby
def fetch_paginated_data(path)
  Enumerator.new do |yielder|
    page = 1

    loop do
      results = fetch_data("#{path}?page=#{page}")

      if results.success?
        # Here's where values are yielded out one at a time
        results.map { |item| yielder << item }
        page += 1
      else
        raise StopIteration
      end
    end
  end.lazy
end
```

So now, the client code looks something like

```ruby
module Names
  class Client
    Name = Struct.new(:id, :name, :births)
    BASE_URL = "http://name-service.com"

    def all_names
      fetch_paginated_data("/users").
        map { |data| convert_to_name(data) }
    end
  end
end
```

### Fully taking advantage of lazy-loading when searching

* Let's say we're searching for data around a specific name - like `Jae`
* We wouldn't want to request *all* the data only to find the data we want was returned in the first request
* So instead, we process each result as it's yielded out of the Enumerator and if it matches what we're looking for, then we stop iteration

```ruby
def find_name(name)
  all_names.detect { |n| n.name == name }
end
```
