# [What is the Walrus operator in Python](https://medium.com/better-programming/what-is-the-walrus-operator-in-python-5846eaeb9d95)

* Walrus operator combines an assignment statement and a boolean expression

```python
if len(some_list) > 3:
  print(f"The list has length {len(some_list)}")
```

* Walrus operator eliminates calling `len` function twice

```python
if (n := len(some_list)) > 3:
  print(f"The list has length {n}")
```

* Parentheses are important
  * Without parentheses `n` will be value of `len(some_list) >3`

