# [14 Advanced Python Features](https://blog.edward-li.com/tech/advanced-python-features/)
## Typing Overloads
* `@overload` is a decorator that allows multiple signatures for the same function
* String Literals = lightweight form of enums

## Generics
* In Python 3.12, new generics syntax, no need for `TypeVar`
* Instead of `Vector: typing.TypeAlias = list[float]` it’s now `type Vector = list[float]`

## Context Managers
* Objects that define enter / exit
* Enter runs in a `with` block, and `exit ` runs when you leave the `with` block (even in exceptional cases)
* Write a function with the `@contextlib.contextmanager` decorator
* Add setup code before `yield` and cleanup code after `yield`

## Structural Pattern Matching
* `match` / `case`
* Can combine with `if` statement
* Can destructure and match tuple values
* Capture collections using `*`

## Slots
* Under the hood, Python classes store instance attributes in an internal dictionary called `__dict__`
* Hash table lookup every time you want to access a value
* `__slots__` uses an array-like structure where attributes can be looked up in “true” `O(1)` time
* Minor speed increase

## `for/else` statements
* Instead of checking if a value was set via a `for` loop, write an `else` clause as part of the `for` loop

## Advanced f-string String Formatting
* Debug Expressions: `print(f”{name=}, {age=}”)` references `name` and `age` variables
* Number formatting
  * `{pi:.2f}` -> 3.14
  * `{big_num:,}` -> `1,000,000`
  * `{num:#0x}` -> `0x1a4`
  * `{num:09}` -> `000000420`
* Date formatting
  * `{now:%Y-%m-%d}`

## Cache/lru_cache
* `@cache` decorator and `@lru_cache` decorator

## Futures
* `concurrent.futures` modules
* Has callbacks like `Promise.then`
* Has methods for handling exceptional cases, setting timeouts, stopping tasks completely
* `asyncio` module allows ability to work with Python’s `async/await` syntax
* `ThreadPoolExecutor` can create and manage tasks
