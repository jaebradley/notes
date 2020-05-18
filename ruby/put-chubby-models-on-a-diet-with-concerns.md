# [Put Chubby Models On A Diet With Concerns](https://signalvnoise.com/posts/3372-put-chubby-models-on-a-diet-with-concerns)

```ruby
module Taggable
  extend ActiveSupport::Concern

  included do
    has_many :taggings, as: :taggable, dependent: :destroy
    has_many :tags, through: :taggings 
  end

  def tag_names
    tags.map(&:name)
  end
end
```

* Concern can be mixed into models that are taggable and single place to update / reason about logic

```ruby
# current_account.posts.visible_to(current_user)
module Visible
  extend ActiveSupport::Concern

  module ClassMethods
    def visible_to(person)
      where \
        "(#{table_name}.bucket_id IN (?) AND
          #{table_name}.bucket_type = 'Project') OR
         (#{table_name}.bucket_id IN (?) AND
          #{table_name}.bucket_type = 'Calendar')",
        person.projects.pluck('projects.id'), 
        calendar_scope.pluck('calendars.id')
    end
  end
end
```

* Concerns are a way of extracting a slice of a model that doesn't seem "part of its essence" - example is a `Dropboxed` `Concern` that is mixed into only the `Person` model that allows routing of incoming emails to right person
  * `Dropboxed` could also just have been stand-alone class
* `current_account.posts.visible_to(current_user)` is a friendlier API than a third query object