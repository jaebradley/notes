# [7 Ways to Decompose Fat ActiveRecord Models](https://codeclimate.com/blog/7-ways-to-decompose-fat-activerecord-models/)

* Extract value objects (like `Date`, `URI`, `Pathname` from Ruby standard lib)
  * Focus on making them immutable
  * Attribute or small group of attributes that have logic associated with them
  * `Rating` class to represent `A-F` letter grades

```ruby
class Rating
  include Comparable

  def self.from_cost(cost)
    if cost <= 2
      new("A")
    elsif cost <= 4
      new("B")
    elsif cost <= 8
      new("C")
    elsif cost <= 16
      new("D")
    else
      new("F")
    end
  end

  def initialize(letter)
    @letter = letter
  end

  def better_than?(other)
    self > other
  end

  def <=>(other)
    other.to_s <=> to_s
  end

  def hash
    @letter.hash
  end

  def eql?(other)
    to_s == other.to_s
  end

  def to_s
    @letter.to_s
  end
end

class ConstantSnapshot < ActiveRecord::Base
  # …

  def rating
    @rating ||= Rating.from_cost(cost)
  end
end
```

* `Rating` has benefits of using `worse_than?` / `better_than?` vs. `<` and `>` operators
* Defining `#hash` and `#eql?` means that `Rating` can be a hash key
* `to_s` to interpolate to a `String`
* Reach for Service Object when action is complex
  * Action touches multiple models
  * Action interacts with external service (posting to social networks)
  * Action is not a core concern of underlying model (like cleaning up outdated data)
* Reach for Query Objects when there are complex queries in definition of `ActiveRecord` (via scopes or class methods)
  * Each query object is responsible for returning a result set

```ruby
class AbandonedTrialQuery
  def initialize(relation = Account.scoped)
    @relation = relation
  end

  def find_each(&block)
    @relation.
      where(plan: nil, invites_count: 0).
      find_each(&block)
  end
end

AbandonedTrialQuery.new.find_each do |account|
  account.send_offer_for_support
end

old_accounts = Account.where("created_at < ?", 1.month.ago)
old_abandoned_trials = AbandonedTrialQuery.new(old_accounts)
```

* Use Policy Objects to represent a business rule (and generally, a single business rule) that are centered around complex read operations
  * Service Objects for write operations and Policy Objects for read operations
  * Query Objects are only focussed on executing SQL to return a result set, whereas Policy Objects operate on domain models already loaded into memory