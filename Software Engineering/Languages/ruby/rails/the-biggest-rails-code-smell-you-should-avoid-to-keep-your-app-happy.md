# [`The Biggest Rails Code Smell You Should Avoid To Keep Your App Happy`](https://medium.com/planet-arkency/the-biggest-rails-code-smell-you-should-avoid-to-keep-your-app-healthy-a61fd75ab2d3)

* Callbacks destroy the linear flow of data
* Without callbacks, you can inspect the flow by starting on the controller phase, going to models, inspecting the model definitions, etc.
* With callbacks, need to check when they are called and then jump to their definition to understand which order they're evaluated in case there are coupled callbacks, then jump back to original method definition
* A general rule of keeping maintainable apps is to favour explicitness over implicitness

## Example of Simplifying Conditional Callbacks

* Here's a `Customer` object that sends a welcome email after creation, but only if the `Customer` was not auto-registered (whatever that means)

```ruby
class Customer < ActiveRecord::Base
    after_create :send_welcome_email, unless: :auto_registered?
    has_one :auto_created, dependent: :destroy

    private

    def send_welcome_email
        CustomerMailer.welcome_email(self).deliver
    end

    def auto_registered?
        !auto_created.nil?
    end
end
```

* Here's a controller for `Customer` creation
* It creates the `Customer`, and attempts to `save` the customer
  * If the `save` is successful it returns a `201` response with the created `Customer` JSON
  * Else it renders the errors and returns a `422`

```ruby
class CustomersController < ApplicationController
    # …
    def create
        @customer = Customer.new(customer_params)

        respond_to do |format|
            if @customer.save
                format.html { redirect_to @customer,
                                            notice: 'Customer was successfully created.' }
                format.json { render :show, status: :created,
                                            location: @customer }
            else
                format.html { render :new }
                format.json { render json: @customer.errors,
                                     status: :unprocessable_entity }
            end
        end
    end
    # …
end
```

* It's not clear looking at the controller what side-effects creating a new `Customer` might trigger
* To make this more obvious at the controller level `Customer` could be written like this
  * The `register` class method `save!`s the customer and then sends the welcome email
  * The `auto_create` class method just `save!`s the customer
  * The implicit side-effects of `Customer` creation have now been split into specific methods that will be explicitly called by the controller

```ruby
class Customer < ActiveRecord::Base
    has_one :auto_created, dependent: :destroy

    def self.register(params)
        new(params).tap do |customer|
            customer.save!
            customer.send_welcome_email
        end
    end

    def self.auto_create(params)
        new(params).tap do |customer|
            customer.save!
        end
    end

    def send_welcome_email
        CustomerMailer.welcome_email(self).deliver
    end
end

class CustomersController < ApplicationController
    # …
    def create
        if auto_create_request?
            @customer = Customer.auto_create(customer_params)
        else
            @customer = Customer.register(customer_params)
        end

        respond_to do |format|
                format.html { redirect_to @customer,
                                            notice: 'Customer was successfully created.' }
                format.json { render :show, status: :created,
                                            location: @customer }
        end
    rescue ActiveRecord::RecordInvalid
        respond_to do |format|
                format.html { render :new }
                format.json { render json: @customer.errors,
                                                         status: :unprocessable_entity }
        end
    end

    def auto_create_request?
        customer_params[:auto_created].present?
    end
    # …
end
```

* So whether or not a welcome email should be sent is explicitly determined at the controller email instead of being an implicit part of the `Customer` creation logic
* These two states (to `auto_create` or to `register`) can be totally eliminated by introducing `register` and `auto_create` `POST` actions
