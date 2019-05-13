# [Lazy Refactoring](https://thoughtbot.com/blog/lazy-refactoring)

* The problem is refactoring logic that searches against an array of columns
* If any of the columns don't return results, default to a fuzzy search
  * `exact_search(column_1) || exact_search(column_2) || exact_search(column_3) || fuzzy_search`
  * Not equivalent to a SQL `OR` as that will return all rows that match any of the criteria

## Custom Enumerator Solution

* How an `Enumerator` works is that the logic inside it's block will run until a value is sent to the `yielder` after `next` is called
* Once a value is sent to the `yielder` it will pause execution until `next` is called again, at which point it will continue executing the defined logic until a value is sent to the `yielder` or a `StopIteration` error is raised

```ruby
example = Enumerator.new do |yielder|
  yielder << "first"
  yielder << "second"
end

example.next # will output "first"
example.next # will output "second"
example.next # will raise StopIteration
```

* Can also loop through enumerator before stopping
* Next example loops through even numbers less than 7 by incrementing until a value is even (and then yielding that value out)
* If number is greater than or equal to 7, a `StopIteration` error is raised, breaking the loop

```ruby
example = Enumerator.new do |yielder|
  yielder << "begin"
  n = 0
  
  loop do
    if n < 7
      yielder << n if n.even?
    else
      raise StopIteration
    end
  n += 1
  end
  
  yielder << "end"
end

example.next # "begin"
example.next # 0
example.next # 2
example.next # 4
example.next # 6
example.next # end
```

* The solution turns the array of columns into an `Enumerator` by calling `each` on the array
* Then in the custom enumerator definition, it loops through the column name enumerator, yielding out a query with the column name
  * Eventually, the column name enumerator will raise a `StopIteration` at the end and the loop will break
  * Finally, the default fuzzy search query is yielded out at the end
* This custom enumerator is used in another query that `detect`s the first value that `exists?`
* This way, only the necessary queries are executed

```ruby
def search(query, column_names)
  results(column_names).detect(&:exists?)
end


def results(column_names)
  columns = column_names.each

  Enumerator.new do |yielder|
    loop do
      yielder << Event.where(columns.next => query)
    end

   yielder << Event.fuzzy_search(query)
  end
end
```

## Using Lazy Enumerators

* Lazy enumerators will pass a value through the entire chain of methods before evaluating next value

```ruby
def search(query, column_names)
  queries = column_names.lazy.map do |column|
    Event.where(column => query)
  end

  queries.detect(&:exists?) || Event.fuzzy_search(query)
end
```
