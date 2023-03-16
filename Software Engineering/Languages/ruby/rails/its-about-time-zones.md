# [It's About Time Zones](https://robots.thoughtbot.com/its-about-time-zones)

* Rails' `ActiveSupport::TimeZone` is a wrapper around `TZInfo` that limits set of time zones provided by `TZInfo` to `146`
  * Displays zones with slightly friendlier names
  * `ActiveSupport::TimeWithZone` has same API as Ruby's `Time` so `Time` and `ActiveSupport::TimeWithZone` instances are interchangeable
* To see all time zones: `rake time:zones:all`
* To see current time zone: `Time.zone`
* To reassign current time zone: `Time.zone = "foo"`
* Set application time zone in `config/appliation.rb` - `config.time_zone = "foo"`
* **Application time zone should be `UTC`**
  * `UTC` is default

## Time zone on User model

```ruby
create_table :users do |table|
  t.string :time_zone, default: "UTC"
end
```

* Store as string since most of Rails' time zone related methods use strings
* **Avoid `enum`s**

## Application Usage

* APIs should use `ISO8601` standard
  * Unambiguous, human-readable, widely-supported, sortable
* Three times to consider
  * System time (`Time.now`)
  * Application time (`Time.zone.now`)
    * Can also use `in_time_zone` to go from system time to application time
  * Database time
    * Rails saves times to database in UTC
    * Always use `Time.current` for database queries so that Rails will translate and compare correct times
* **Do Use**
  * `Time.current`
  * `2.hours.ago`
  * `Time.zone.today`
  * `Date.current`
  * `1.day.from_now`
  * `Time.zone.parse("2015-07-04 17:05:37")`
  * `Time.strptime(string, "%Y-%m-%dT%H:%M:%S%z").in_time_zone`
* **Don't Use**
  * `Time.now`
  * `Date.today`
  * `Date.today.to_time`
  * `Time.parse("2015-07-04 17:05:37")`
  * `Time.strptime(string, "%Y-%m-%dT%H:%M:%S%z")`

## Testing

* Rails `4.1` added `ActiveSupport::Testing::TimeHelpers`
  * Has three useful methods: `travel`, `travel_back`, `travel_to` to freeze blocks of time
  * There's also `Timecop` (`.freeze`, `.travel`, `.return` (to clean up after a spec and return to current time))
    * Can also use blocks like `Time.use_zone("Sydney") do ... end`
