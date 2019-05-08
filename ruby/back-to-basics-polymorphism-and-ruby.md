# [Back to Basics: Polymorphism and Ruby](https://thoughtbot.com/blog/back-to-basics-polymorphism-and-ruby)

* In Ruby, it means being able to send the same message to different objects and get different results
* Inheritance-based Polymorphism
  * Create a base class that has a method that raises `NotImplementedError`
  * Create another class that inherits from this base class with some type of implementation
  * In this way, can create many classes that will behave differently depending for same method
* Duck Typing
  * No base class but rather two classes (`XmlParser` and `JsonParser`)
  * Then there's a `GenericParser` class that has a method that receives a parser method argument and calls the `parse` method on the parser method argument
  * In the example below, only thing that `GenericParser` depends on is that the parser method argument responds to `parse` message
  * As before, method behaves differently based on which parser is specified

```ruby
class GenericParser
  def parse(parser)
    parser.parse
  end
end

GenericParser.new.parse(XmlParser.new)
```

* Decorator Pattern
  * For each specific parser implementation a generic parser is passed to the constructor
  * This generic parser is then called in each specific implementation's parse method

```ruby
class GenericParser
  def parse
  end
end

class JsonParser
  def initialize(parser)
    @parser = parser
  end

  def parse
    # some other logic
    @parser.parse
  end
end

class XmlParser
  def initialize(parser)
    @parser = parser
  end
  
  def parse
    # some XML specific logic
    @parser.parse
  end
end
```

