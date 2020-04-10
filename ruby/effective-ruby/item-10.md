# Item 10: Prefer `Struct` to `Hash` For Structured Data

* Imagine class that returns an array of hashes
* Downside is that the keys for each hash are an internal implementation detail
  * API users would have to read entire definition of the initialize method to know which keys are populated within which CSV columns
  * Might not be a burden when method is only setting keys, but with time, it may become more of a burden to read (i.e. have to go back and remind yourself what keys are available)
* Might even want to perform some calculations on each hash in the array - but would be better if each hash / object in array could do these calculations on their own so that logic could be abstracted away
* Using hashes to stand-in for really simple classes tends to happen a lot in Ruby - sometimes it's fine but often times there should be dedicated types for these objects
* By using a `Struct` - typos in attribute names will raise `NoMethodError` exceptions instead of getting `nil` back for an invalid hash key name
  * Can also define methods on a `Struct` inside a block

```ruby
Struct.new(:date, :high, :low) do
  def mean
    (high + low) / 2.0
  end
end
```