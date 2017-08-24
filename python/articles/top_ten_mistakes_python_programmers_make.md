# [Top Ten Mistakes Python Programmers Make](https://www.toptal.com/python/top-10-mistakes-that-python-programmers-make)

## Misusing Expressions as Default Arguments

Consider

```python
def foo(bar=[]):
  bar.append('baz')
  return bar
```

Calling `foo` multiple times will result in some (potentially) unexpected behavior

```python
foo() # ['baz']
foo() # ['baz', 'baz']
foo() # ['baz', 'baz', 'baz']
```

This occurs because the default value for a function argument is only evaluated
at the time the function is defined.

This means that `bar` is initialized when `foo` is first called, but subsequent
calls will use the same `bar`.

That's why default arguments use `None`.

Consider

```python
class A(object):
    x = 1

class B(A):
    pass

class C(A):
    pass

print A.x, B.x, C.x # 1, 1, 1

B.x = 2

print A.x, B.x, C.x # 1, 2, 1

A.x = 3

print A.x, B.x, C.x # 3, 2, 3
```

`C.x` changes from `1` to `3` because attribute `x` is not found in class `C` but
is found in the base class `A`.

## Catching Multiple Exceptions

Do

```python
try:
  # stuff
except (SomeError, AnotherError) as e:
  # handle here
```

## Assignment to a Variable in a Scope

Consider

```python
x = 10
def foo():
  x += 1

foo() # this will throw an exception
```

`foo()` throws an exception because when you make an assignment to a variable,
that variable is considered to be local to that scope. Since `x` is presumed to
be in the local scope, but hasn't yet been defined, an exception is thrown.

## Binding of Variables in a Closure

Consider

```python
def create_multipliers():
    return [lambda x : i * x for i in range(5)]
for multiplier in create_multipliers():
    print multiplier(2)
```

You might expect the output to be `0`, `2`, `4`, `6`, `8`.

But the actual output is `8`, `8`, `8`, `8`, `8`.

This is because of Python's late-binding behavior which means that variable values
in closures are looked up at the time the inner function is called. Thus, when
any of the returned functions are called, `i` is `4` because the loop has completed
and so it's value in the given scope at that point in time is `4`.

The solution is to use default arguments

```python
def create_multipliers():
    return [lambda x, i=i : i * x for i in range(5)]
```
