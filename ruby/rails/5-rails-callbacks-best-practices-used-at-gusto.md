[`5 Rails Callbacks Best Practices Used at Gusto`](https://engineering.gusto.com/the-rails-callbacks-best-practices-used-at-gusto/)

# Omit When Possible

* Callbacks are used to ensure that when an action is performed, some side-effect is not forgotten
* Gusto is moving towards using service objects to encapsulate this side-effect logic
* In the following example, an email is sent after the record has been committed
  * There are a couple downsides to this approach
  * It adds logic that will run on every `save` (even though it doesn't need to)
  * It couples the `Company` record with `EmailSender`

```ruby
class Company < ActiveRecord::Base
  after_commit :send_emails_after_onboarding

  private

  def send_emails_after_onboarding
    if just_finished_onboarding?
      EmailSender.send_emails_for_company!(self)
    end
  end
end
```

* However, this logic could be abstracted out to a `CompanyOnboarder` class like

```ruby
class Company < ActiveRecord::Base
end

class CompanyOnboarder
  def onboard!(company_params)
    company = Company.new(company_params)
    company.save!
    EmailSender.send_emails_for_company!(company)
  end
end
```

# Asynchronous By Default

* Every callback is an extra layer of computation
* Thus, these callbacks will slow down the round trip time for requests
* Callbacks should be as asynchronous as possible (usually means enqueuing a `SideKiq` job)
* Here's an example of adding a blocking database operation as an `after_commit` callback

```ruby
class Company < ActiveRecord::Base
  after_commit :create_welcome_notification, on: :create

  private

  def create_welcome_notification
    # We're incurring an extra database operation here, which
    # is something we want to avoid during
    # critical operations like signing up a
    # new customer
    notifications.create({ title: 'Welcome to Gusto!' })
  end
end
```

* Instead, a `SideKiq` worker (something like `WelcomeNotificationCreator`) job should be enqueued that will generate the welcome notification

```ruby
class Company < ActiveRecord::Base
  after_commit :create_welcome_notification, on: :create

  private

  def create_welcome_notification
    WelcomeNotificationCreator.perform_async(id)
  end
end

class WelcomeNotificationCreator
  include Sidekiq::Worker

  def perform(company_id)
    @company = Company.find(company_id)
    @company.notifications.create({ title: 'Welcome to Gusto!' })
  end
end
```

## How to test this callback

* Interesting that the `subject` is the `company.save` operation - I've not been doing that and I should
* Also interesting is the expectation that looks at the job's `args` and makes sure it equals the saved `company`'s `id`
* Same thing for `WelcomeHOtificationsCreator` - it tests that `Notification`s have updated
  * I'd probably check the notifications on that particular `company`

```ruby
# spec/models/company_spec.rb
require 'rails_helper'

RSpec.describe Company, type: :model do
  describe '#save' do
    subject { company.save }
    let(:company) { build(:company) }

    it 'schedules a WelcomeNotificationCreator job' do
      expect {
        subject
      }.to change{ WelcomeNotificationCreator.jobs.size }.by(1)
      last_job = WelcomeNotificationCreator.jobs.last
      expect(last_job['args']).to eq([subject.id])
    end
  end
end

# spec/workers/welcome_notification_creator_spec.rb
require 'rails_helper'

RSpec.describe WelcomeNotificationCreator do
  subject { described_class.new.perform(company.id)}
  let(:company) { create(:company) }

  it 'creates a notification' do
    expect {
      subject
    }.to change(Notification, :count).by(1)
    expect(Notification.last.title).to eq('Welcome to Gusto!')
  end
end
```

# Prefer `after_commit` to `after_save`

* `after_commit` is the only callback that is triggered after a database transaction is committed
* If you don't use the `after_commit` callback, you could see weird state like `Cannot find Company with ID = 1234`
  * `Sidekiq` is attempting to `perform` the job before the record has been committed to the database

# Avoid conditional execution / move conditional execution to `Sidekiq`

* Add conditional logic in method body and not in callback signature
* It's easier for conditional to grow in method body and not in callback
* In this example, using conditional in callback signature to trigger `Sidekiq` job

```ruby
class Company < ActiveRecord::Base
  after_commit :create_welcome_notification, if: -> { should_welcome? && admirable_company? }

  private

  def create_welcome_notification
    WelcomeNotificationCreator.perform_async(id)
  end
end
```

* A better approach might be to move the conditional (`should_welcome?`) to the `create_welcome_notification` method

```ruby
class Company < ActiveRecord::Base
  after_commit :create_welcome_notification

  private

  def create_welcome_notification
    if should_welcome? && admirable_company?
      WelcomeNotificationCreator.perform_async(id)
    end
  end
end
```

* Gusto's recommended approach is to move the conditional to the `Sidekiq` job
* Downside is that it might create a lot of `no-op` jobs

```ruby
class Company < ActiveRecord::Base
  after_commit :create_welcome_notification

  private

  def create_welcome_notification
    WelcomeNotificationCreator.perform_async(id)
  end
end

class WelcomeNotificationCreator
  include Sidekiq::Worker

  def perform(company_id)
    @company = Company.find(company_id)

    return unless @company.should_welcome? && @company.admirable_company?

    # We passed our check, do the work.
  end
end
```
