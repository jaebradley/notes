# [Faking APIs in Development and Staging](https://robots.thoughtbot.com/faking-apis-in-development-and-staging)

## Faking in Tests

* Use `movie-facts.com` API
* Build class that calls this API using `HTTParty`

```ruby
# app/models/movie_database.rb

class MovieDatabase
  BASE_URL = "http://api.movie-facts.com".freeze

  def actors_for(movie:)
    HTTParty.get(BASE_URL + "/movies/#{movie.id}/actors", format: :json)
  end
end
```

* Webmock will error saying that external requests are blocked in tests
* Build test fake of API using `Sinatra`

```ruby
# spec/support/fake_movie_facts.rb

module FakeMovieFacts
  class Application < Sinatra::Base
    get "/movies/:movie_name/actors" do
      {
        actors: [
          {
            name: "Actor 1",
            character_played: "Character 1"
          },
          {
            name: "Actor 2",
            character_played: "Character 2"
          }
        ]
      }.to_json
    end
  end
end
```

* Advantage of using Capybara Discoball is that requests run to actual service on localhost
* Have to configure Capybara Discoball

```ruby
# spec/support/fake_movie_facts_runner.rb

FakeMovieFactsRunner =
  Capbybara::Discoball::Runner.new(FakeMovieFacts::Application) do |server|
    MovieDatabase.base_url = "#{server.host}:#{server.port}"
  end
```

* Have to also configure the service to run in `spec_helper`

```ruby
# spec/spec_helper.rb

FakeMovieFactsRunner.boot
```

## Faking in Development

* Access to `movie-facts.com` API is limited because contract / access is being finalized so trying to call API is `401`ing
* Could use fake service in development as well (in order to fast-track work on feature that depends on this API)
* `ruby spec/support/fake_movie_facts.rb` boots up Sinatra server on `localhost:4567`
* Now need to point `MovieDatabase` to this local service (use environment variables to set base path)

## Faking in Staging

* Need to run both main application and the fake application so that they can talk to each other
* Simplest way is that both are deployed on Heroku and connect via HTTP (like they would in real-life)
* How to extract fake to own application AND provide allow tests to pass for main application?
  * Extract fake service to separate repo
  * Wrap it in a gem so it can be used in tests
  * Provide a `config.ru` in the root of the gem so it can be deployed to Heroku

```ruby
# config.ru

$LOAD_PATH << File.expand_path("../lib", __FILE)
require "fake_movie_facts/application"

run FakeMovieFacts::Application
```

## Faking Webhooks

* Let's say, there's an API that triggers some event webhook on an action (like subscription)
* When faking this service, trying to trigger the webhook before returning the faked successful subscription JSON will deadlock
  * This is because the webhook callback will not succeed until the callback URL is processed (by the main app)
  * But this callback request won't be processed until the faked service request succeeds since everything is synchronous
  * Need to asynchronously trigger webhook using another thread (or maybe a background process) in this faked endpoint
