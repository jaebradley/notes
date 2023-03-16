# [Meditations on a Class Method](https://robots.thoughtbot.com/meditations-on-a-class-method)

```ruby
def self.run(user)
  new(user).run
end
```

* A beautiful class method - instantiate the class and then call a method on the instance
* If you're calling class method you probably want
  * a new instance of the class
  * or convenience

## Example of what not to do

```ruby
class ImageUploader
  def self.run(xpm)
    @@dimensions = geometry_for(xpm)
    @@color_palette = colors_for(xpm)
    svg = generate_svg(xpm)
  end

  def self.geometry_for(xpm)
    # ...
  end

  def self.colors_for(xpm)
    # ...
  end

  def self.generate_svg(xpm)
    # ...
  end
end
```

* Class variables are not thread-safe
* Code that is exposed

## Goals

* Smallest interface possible
  * Limits options for user
  * Increases the amount of implementation that is hidden which allows more flexibility to change internals going forward
* The `run` class method abstracts away if the constructor changes or if the `run` instance method changes
* Use abstractions at the same level - create an instance and call a method
  * Perhaps, this instance method creates some objects and calls methods on those objects
  * Regardless, it's short, concise methods all the way down
