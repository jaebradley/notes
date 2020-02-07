# [Everything You Always Wanted to Know About Writing Good Rake Tasks But Were Afraid to Ask](https://edelpero.svbtle.com/everything-you-always-wanted-to-know-about-writing-good-rake-tasks-but-were-afraid-to-ask)

* Write meaningful descriptions
  * if you can't explain rake task in one sentence, probably means rake task is doing too much and should be split up

```ruby
# convert this...
task import_topics: :environment do
  ...
end

# ...to this
desc 'Migrate topics from legacy database to new database'
task import_topics: :environment do
  ...
end
```
* Group tasks using namespaces
  * Instead of having `n` `migrate_x` rake tasks, have a `migrate` namespace that has each _type_ of migration as a separate task
  * So convert `migrate_topics`, `migrate_users`, and `migrate_questions` tasks to live under the `migrate` namespace with a `topics`, `users`, and `questions` task
* In a similar vein, instead of having a single `tasks` directory with `x` rake tasks, can split out the file structure to mirror the namespacing
  * So from example above, there is a `tasks/migrations` directory with a `topics.rake`, a `users.rake`, and a `questions.rake`
* Refactor task into a class or service object
  * This allows for code de-duplication and testing
* Always use a log file - `ActiveSupport::Logger`
  * Track start / end and any relevant task information
  * In addition to logging to file, should also print output so you know rake task is actually working
