# [Debugging Why Your Specs Have Slowed Down](https://robots.thoughtbot.com/debugging-why-your-specs-have-slowed-down)

* A large `spec_helper` file might be indicative of potential culprits
* Look for `before` blocks like this one
  * What it's doing is running `stub_something` before each test

```ruby
config.before(:each) do
  stub_something
end
```

* Could filter further by specifying it should run only for `feature` tests like `config.before(:each, type: :feature)`
  * This could still mean that the logic runs for unnecessary tests
  * A better approach could be doing something like `config.before(:each, :stub_something)` and then in the spec do something like `scenario "user does something", :stub_something`
* `DatabaseCleaner.strategy = :truncation` is slow compared to the `transaction` strategy
* Use `--profile` flag to identify the slowest specs (`rspec spec/models/user_spec.rb --profile 5`)
* Unnecessary `create`s vs. `build_stubbed`s?
  * Check in factory files to see if `create`s can be moved to traits
* This configuration outputs the records that are created / built per test

```ruby
# spec/spec_helper.rb

config.before(:each, :monitor_database_record_creation) do |example|
  ActiveSupport::Notifications.subscribe("factory_girl.run_factory") do |name, start, finish, id, payload|
    $stderr.puts "FactoryGirl: #{payload[:strategy]}(:#{payload[:name]})"
  end
end
```
