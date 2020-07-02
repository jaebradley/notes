# [Eliminating Flaky Ruby Tests](https://engineering.gusto.com/eliminating-flaky-ruby-tests/)

## Randomize Test Ordering

* RSpec can provide randomized test ordering via the `--seed` flag
  * By default, `RSpec` runs tests in their defined order
* Using random test values like with `Faker` can cause unwanted randomness

## Be Careful When Using Randomly Generated Test Data

```ruby
let(:instance) { build(:some_model, email: Faker::Internet.email) }
let(:new_email) { Faker::Internet.email }

subject { instance.save! }

it 'sends an email' do
  instance.email = new_email
  expect{ subject }.to change(ActionMailer::Base.deliveries, :count).by(1)
end
```

* Will work most times, but `new_email` may be the same as `instance.email` - in this case, an email would _not_ be sent
* Also applies to using factories with `FactoryBot`

```ruby
factory(:user) do 
  sequence(:email) { |n| "#{n}_#{Faker::Internet.email}" }
end
```

## Globally Cached Values

```ruby
# state_checker.rb
class StateChecker
  UNSUPPORTED_STATES = [‘CA’, TX’]
  
  def self.supported_states
    @supported_states ||= STATES — UNSUPPORTED_STATES
  end
end

# state_checker_spec.rb
describe StateChecker
  describe ‘.supported_states’ do
    before do
      stub_const(‘STATES’, [‘AA’, ‘BB’, ‘CC’, ‘DD’])
      stub_const(‘StateChecker::UNSUPPORTED_STATES’, [‘AA’, ‘BB’])
    end
    it ‘removes the unsupported states’ do
      expect(StateChecker.supported_states).to match_array %w(CC DD)
    end
  end
end
```

* Any subsequent tests using `StateChecker.supported_states` will read the stubbed values instead of the actual values because the class instance variables were not cleared
* Avoid stubbing when testing memoized methods - if it is unavoidable make sure to reset the memoized methods
