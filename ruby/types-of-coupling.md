# [Types of Coupling](https://robots.thoughtbot.com/types-of-coupling)

## Pathological Coupling

* A class reaching into another class and reads / changes it's instance variables
* Monkey-patching falls into this category

```ruby
class NuclearLaunchController
  def initialize(launch_codes)
    @launch_codes = launch_codes
  end
end

class ExtremelyBadIdea
  def initialize(nuclear_launch_controller)
    @launch_controller = nuclear_launch_controller
  end

  def do_bad_things
    # This is poison
    @launch_controller.instance_variable_set(:@launch_codes, 'password')
  end
end
```

## Global Coupling

* Classes that both rely on some shared global state
  * state could be a Singleton or a class variable
* For example, test files that depend on global factory definitions - any changes to these definitions will be global
  * In this case, the cure (duplicating a ton of test code) is probably worse than the disease

## Control Coupling

* Passing in a flag that tells method what to do
* The coupling here is that the external caller often has some insight into how the method is implemented via this parameter
* An example is `save(false)` in `ActiveRecord` (or `save(validate: false)`)
  * Caller now has some insight that some type of validation may or may not execute based on the passed-in boolean
  * One possible solution is to split into two methods: `save` and `save_without_validations`

## Data Coupling

* Pass in a method parameter and then use some method (`to_s`, for example) on that instead of the parameter itself
* This coupling isn't *that* bad

```ruby
class ScreenPrinter
  # This method is coupled to its parameter, because a change to that argument
  # can cause breakage (if we undefined to_s, for example).
  def print(text)
    output_to_screen(text.to_s)
  end
end
```
