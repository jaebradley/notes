# [Performing Custom Validations In Rails](https://hackernoon.com/performing-custom-validations-in-rails-an-example-9a373e807144)

* `Shipments` table where each row represents a package with a `width`, `height`, `depth`, and `weight`
* Need to validate these values
  * Volume must be between `20` and `4000` cubic centimeters
  * Density must exceed `200` grams per cubic centimeters
  * Side length cannot be less than `10%` of largest side

```ruby
class Shipment < ActiveRecord::Base
  …
  validate :volume_limits
  …
  private
  def volume_limits
    if volume > 4000
      errors.add(:volume, “cannot be above 400 cubic inches”)
    elsif volume < 20
      errors.add(:volume, “cannot be below 20 cubic inches”)
    end
  end
end
```

* Extract density validation logic to helper class (`DensityValidator`)

```ruby
class Shipment < ActiveRecord::Base
  …
  validates_with DensityValidator
  …
end

class DensityValidator < ActiveModel::Validator
  def validate(record)
    if record.density > 20
      record.errors.add(:density, “is too high to safely ship”)
    end
  end
end
```

* Extract side length validator
  * Each package side must be validated separately
  * The `package_proportion` flag means that the model expects a validator called `PackageProportionValidator`
  * `PackageProportionValidator` extends `ActiveModel::EachValidator`
    * `validate_each` provides access to individual attribute being validated and the entire record

```ruby
class Shipment < ActiveRecord::Base
  ...
  validates :height, :width, :depth, package_proportion: true
  ...
end

class PackageProportionValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    if value < [record.width, record.height, record.depth].max * 0.1
      record.errors.add(attribute, "cannot be so short as to make
      the package oddly sized :(")
    end
  end
end
```
