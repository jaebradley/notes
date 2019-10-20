# Chapter 8: Combining Objects with Composition

* Composition is a "has a" relationship - bicycles have parts, for example
* `Bicycle` would ask `Parts` for `spares`

```ruby
class Parts
  attr_reader :chain, :tire_size
  
  def spares
    { tire_size: tire_size,
      chain: chain }.merge(local_spares)
  end

  def default_tire_size
    raise NotImplementedError
  end

  def local_spares
    {}
  end

  def default_chain
    '10-speed'
  end
end

class RoadBikeParts < Parts
  def local_spares
    { tape_color: tape_color }
  end

  def default_tire_size
    '23'
  end
end
```
* `Parts` begs the question of being composed by many `Part` objects
* `Parts#spares` calls each `Part#needs_spare`

```ruby
class Parts
  def spares
    parts.select{ |part| part.needs_spare }
  end
end
```

* `Parts` seems array-like, at the very least
  * Though having it fully extend `Array` might lead to unexpected behavior (adding `Parts` together returns `Array`s and not `Parts`)
  * `Parts` extends `Forwardable` and includes `Enumerable`
  * Including `Enumerable` gets common traversal and searching methods

## Parts Factory

* An object that manufactures other objects is a factory
* Objects should generally always be created through factory or else logic in factory will be duplicated
* Can replace `Part` with `OpenStruct`

```ruby
require 'ostruct'

module PartsFactory
  def self.build(config, parts_class = Parts)
    parts_class.new(
      config.collect{ |part_config| create_part(part_config) }
    )
  end

  def self.create_part(part_config)
    OpenStruct.new(
      name: part_config[0],
      description: part_config[1],
    )
  end
end
```

* classes for "is-a", duck types for "behaves-like-a", composition for "has-a"
