# [Testing Third Party Interactions](https://robots.thoughtbot.com/testing-third-party-interactions)

## Stubbing the adapter

* You have service class that abstracts third-party interactions
* Stub this service class so that you don't have to make HTTP requests and instead returns some expected response
* Testing `ShoppingCart`'s calculation that involves calling a third-party `QuoteService`
* Useful for unit-testing objects that depend on data from other services

```ruby
require "rails_helper"

describe ShoppingCart do
  it "calculates tax" do
    item = create(:item)
    shopping_cart = ShoppingCart.new(item)

    allow(QuoteService).to receive(:fetch).and_return(100)

    expect(shopping_cart.tax).to eq 10
  end
end
```

## Stubbing the network

* Want `QuoteService` to execute but don't want to make the HTTP request
* `QuoteService` *thinks* its making HTTP requests but instead it's getting some pre-determined response
* Can do this by
  * stubbing the HTTP library (`HTTParty` or `Net::HTTP`)
  * Webmock gem
  * VCR gem that records real API responses and then replays these responses

```ruby
# Webmock example

require "rails_helper"

describe ShoppingCart do
  it "calculates tax" do
    item = create(:item)
    shopping_cart = ShoppingCart.new(item)

    stub_request("http://quote-service.com/prices").and_return(
      { pricesRequest: { regularPrice: 100 } }.to_json
    )

    expect(shopping_cart.tax).to eq 10
  end
end
```

* Previous test does test both the `ShoppingCart` and underlying `QuoteService` so it's not a pure unit test
* It's more of an integration / feature test

## Swapping out server

* Build fake service (using Sinatra)
* Point tests to use fake service
* If fake service is turned into a gem then it can be shared and reused
* Makes real HTTP requests to a local service
* Useful for end-to-end tests
