# Item 30: Prefer `define_method` to `method_missing`

## Proxies

* Consider this proxy class that wraps a `Hash` and uses `method_missing` to forward all undefined methods to `Hash` class
  * If `@hash` responds to the method then it calls the method else, it calls super so that `BasicObject` class can raise `NoMethodError`

```ruby
class HashProxy
  def initialize
    @hash = {}
  end

  private

  def method_missing(name, *args, &block)
    if @hash.respond_to?(name)
      @hash.send(name, *args, &block)
    else
      super
    end
  end
```

* Using `method_missing` doesn't expose the interface for `HashProxy` at all
  * Calling `respond_to?(:size)` will return `false`
  * And there won't be any public methods that are also exposed by `Hash` class
* If you're not using the `Forwardable` module (and you probably should be), use `define_method` (which is essentially what `Forwardable` module is doing anyways)

```ruby
class HashProxy
  Hash.public_instance_methods(false).each do |name|
    define_method(name) do |*args, &block|
      @hash.send(name, *args, &block)
    end
  end

  def initialize
    @hash = {}
  end
end
```

* Iterates over public instance methods in `Hash` class
  * Instance method is created in `HashProxy` using `define_method` for each of the public instance methods - these `HashProxy` instance methods, simply forward to `Hash` public instance methods
  * Benefit is that it exposes `Hash` interface - `respond_to?(:size)` will return `true` and `HashProxy#public_methods(false)` will return `Hash` public methods

## Decorators

* Classes implementing the decorator pattern wrap an arbitrary object and extend its capabilities in some way
  * Unlike the `HashProxy` class, which knew that we'd be forwarding messages to a hash object, the decorator class accepts any objects of any class and needs to deletegate to them appropriately

```ruby
class AuditDecorator
  def initialize(object)
    @object = object
    @logger = Logger.new($stdout)
  end

  private

  def method_missing(name, *args, &block)
    @logger.info("calling '#{name}' on #{@object.inspect}")
    @object.send(name, *args, &block)
  end
end
```

* This `AuditDecorator` simply logs entries before delegating to the target class
  * `AuditDecorator` has similar problem as before where it doesn't respond to introspective methods like `respond_to?` and `public_methods`
  * It also doesn't forward all methods like `class` - it will print `AuditDecorator`
* To solve this, the `initialize` method needs to inspect the object it's wrapping and create the appropriate forwarding methods
  * Methods can't be instance methods for the `AuditDecorator` class but instaed, must be created dynamically for every single instance of `AuditDecorator` since they must be able to wrap different objects with different classes

```ruby
class AuditDecorator
  def initialize(object)
    @object = object
    @logger = Logger.new($stdout)

    mod = Module.new do
      object.public_methods.each do |name|
        define_method(name) do |*args, &block|
          @logger.info("calling '#{name}' on #{@object.inspect}")
          @object.send(name, *args, &block)
        end
      end
    end

    extend(mod)
  end
end
```

* To generate the dynamic methods for each `AuditDecorator` instance, need to create an anonymous module and define methods we want for that instance, inside the anonymous module and then extend the anonymous module, copying all generated methods into the instance
  * Note that the anonymous module references `object.public_methods` but then inside the `each` block, calls `@object`
  * This is because inside the module, but not when inside a method definition, `@object` refers to a module variable and not the instance variable defined in `AuditDecorator`
  * However, the `new do` block does form a closure that allows access to the local variables
* `define_singleton_method` has same effect as definining method in an anonymous module that is then extended

```ruby
@object.public_methods.each do |name|
  define_singleton_method(name) do |*args, &block|
    # blahblahblah
  end
end
```