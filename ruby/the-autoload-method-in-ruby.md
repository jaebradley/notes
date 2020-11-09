#[The Autoload Method In Ruby](https://medium.com/rubycademy/the-autoload-method-in-ruby-11fd079562ef)

* The `Module#autoload` method registers a file path to be loaded the first time that a specified module or class is accessed in the namespace of the calling module or class

```ruby
# car.rb

module Car
  autoload(:Engine, './engine.rb')

  puts "Engine module isn't loaded"

  Engine

  puts "Engine module is loaded"
end

# engine.rb

module Engine
  puts "Loading the Engine module"
end
```

* The `Engine` module is only loaded (i.e. it will only be executed) when it is called for the first time
  * This allows Ruby to only load files that contain modules / classes that are used by the execution flow of the running program
* When `autoload` is called, the `module/class` + the file path are stored in an internal hash table

```ruby
# a.rb

module A
  autoload(:B, './b.rb')

  p constants
end

# b.rb

module B

end
```

* The above will produce `[:B]`
* The call to `autload` automatically creates a constant with the first argument, and flagged as "registered"
* When the `B` module is invoked, Ruby will look for the `B` entry within the `constants` hash table of the `A` namespace
  * Then it will load the file using `Kernel#require`
