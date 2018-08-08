# [5 Ruby on Rail Tips You Probably Didn't Know](https://hackernoon.com/5-ruby-on-rails-tips-you-probably-dont-know-8b80b4a0890f)

### `Hash#dig`

```ruby
# Replace this
params[:user] && params[:user][:address] && params[:user][:address][:somewhere_deep]

# With this
params.dig(:user, :address, :somewhere_deep)
```

### `Object#presence_in`

```ruby
# Replace this
options.include?(value) ? value : default

# Or this
(options.include?(value) && value) || default

# With this
value.presence_in(options) || default
```

### `Object#presence`

* Equivalent to `object.present? ? object : nil`

### `Module.delegate`

* Encourages loose coupling

```ruby
class Profile < ApplicationRecord
  belongs_to :user

  delegate :email, to: :user
end

# profile.email is equal to profile.user.email
```
