# [Data Migrations In Rails](https://robots.thoughtbot.com/data-migrations-in-rails)

* Rails description of migrations

> Migrations are a feature of Active Record that allows you to evolve your database schema over time.
> Rather than write schema modifications in pure SQL, migrations allow you to use an easy Ruby DSL to describe changes to your tables.

* Note does not talk about data manipulation
* Migrations should purely be about schema changes
* Manipulating data in migrations is a bad idea
  * Data migrations will stay in `db/migrations` forever and will run whenever a new developer sets up local environment
  * Future changes to a class could break a migration later
  * Data migrations can be ignored by running `rake:db:schema:load` or `rake:db:reset` - both commands load latest version of schema without touching migrations
  * Application deployment is now dependent on data migration - this is more of a problem with larger db with millions or rows

## Temporary Rake Tasks

* Rake tasks decouple deployment from completed migrations
* Downsides
  * Remembering to add / run rake tasks
  * Remembering to remove rake tasks after completion

```ruby
# lib/tasks/temporary/users.rake
namespace :users do
  task :set_newsletter => :environment do
    User.all.each do |user|
      if user.confirmed?
        user.receive_newsletter = true
        user.save
      end
    end
  end
end
```

* Issues with task
  * Gets all users instead of batching through them
  * Invokes validations and callbacks, which may have unintended consequences
  * Uses `if` block to check if user needs to updated
  * Gives no indication that job is running
  * No description, so we won't see task running when executing `rake -T`

```ruby
# lib/tasks/temporary/users.rake
namespace :users do
  desc "Update confirmed users to receive newsletter"
  task set_newsletter: :environment do
    users = User.confirmed
    puts "Going to update #{users.count} users"

    ActiveRecord::Base.transaction do
      users.each do |user|
        user.mark_newsletter_received!
        print "."
      end
    end

    puts " All done now!"
  end
end
```

* Improvements
  * Includes a description so will be able to see task and description when running `rake -T`
  * Uses a scope to fetch only the users needed to be updated
  * `puts` statement gives visual indication that job is running and how many users are going to be updated
  * Wraps changes in a transaction
