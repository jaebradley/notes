# Item 34: Consider Supporting Differences In `Proc` Arity

* The most idiomatic way to create a `Proc` object is by passing a block to a method - while the block is just Ruby syntax, it eventually gets wrapped up in a `Proc` and passed to the method
* `Proc`s can be divided into "weak" and "strong" `Proc`s where the main difference between the two is how they deal with invalid arguments
  * Calling a "weak" `Proc` with the wrong number of arguments does not raise an exception or produce a warning
  * Calling a "weak" `Proc` with too few arguments, sets the extra ones to `nil` - too many arguments, and the extras are ignored
  * A "strong" `Proc` obeys the rules of normal method calls - if the arguments aren't exactly correct, an `ArgumentError` exception is raised
* Blocks turn into "weak" `Proc`s and lambdas are "strong" `Proc`s
  * Can distinguish between "weak" and "strong" `Proc`s by calling `lambda?` method - `false` for "weak" and `true` for "strong"
* Imagine building a `Stream` class with a `stream` instance method that takes a `Proc`
  * As it's reading data, it will pass to the `Proc` both the data that was read and the time it took to read this data
  * A weak `Proc` will just silently ignore this data if it doesn't want / need both arguments
  * However, this doesn't work for `Proc`s (or methods that get converted to "strong" `Proc`s using `&` operator within a method invocation) that take a single argument
  * Use case in `Item 34` is if `Stream#stream` was used to generate a hash via the `Digest::SHA256` class
    * `SHA256` has an instance method, `update`, where you can supply data in chunks
    * So this works well with `Stream` where the data can be sent to `SHA256#update` - however, since `Stream#stream` outputs two arguments and `SHA256#update` only takes one argument, this will raise an exception

```ruby
class Stream
  def stream(&block)
    loop do
      start = Time.now
      data = @io.read(@chunk)
      return if data.nil?

      time = (Time.now - start).to_f
      block.call(data, time)
    end
  end
end

# In another class
def file_size(file)
  File.open(file) do |f|
    bytes = 0

    s = Stream.new(f)
    s.stream{ |data| bytes += data.size }

    bytes
  end
end

# Doesn't work with SHA256
# In yet another class
def digest(file)
  File.open(file) do |f|
    sha = Digest::SHA256.new
    s = Stream.new(f)
    s.stream(&sha.method(:update))
    sha.hexdigest
  end
end
```

* `Proc#arity` returns a `Fixnum` that describes the number of arguments the `Proc` object expects
  * When the `Fixnum` is positive, it returns the number of required arguments
  * The `Fixnum` can be negative, when there are default arguments or variadic arguments using `*`
  * If a method had one mandatory argument and one optional argument, it's arity would be `-2`
    * Can use unary complement operator (`~`) to turn the result into number of required arguments
    * Two's complement is calculated by flipping the bits of a number and adding one
      * Negative numbers are stored as two's complement of positive counterpart
      * So to get `-2`, `+2` starts like `0000 0010` => `1111 1101` (inverting bits) => `1111 1110` (adding one)
      * However, it's just called a "complement" when flipping the bits
      * Flipping the bits of `-2` (`1111 1110`) results in `0000 0001`, which is `1`

```ruby
def stream(&block)
  loop do
    start = Time.now
    data = @io.read(@chunk)
    return if data.nil?

    arg_count = block.arity
    arg_list = [data]

    # if expected required args is two, add the timing data
    if arg_count == 2 || ~arg_count == 2
      arg_list << (Time.now - start).to_f
    end

    block.call(*arg_list)
end
```