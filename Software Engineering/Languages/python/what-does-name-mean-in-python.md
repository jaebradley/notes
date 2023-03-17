# [What Does Name Mean In Python](https://medium.com/code-python/what-does-name-mean-in-python-c3f80377b46b)

```python
def main():
  # some logic

if __name__ == '__main__':
  main()
```

* If a file is run by itself, then `__name__` will be `__main__`
* If a file is imported and `imported_file.__name__` is called, the name of the file is returned
* This allows the functionality of creating scripts that can be run by itself, but also can be imported into other files
