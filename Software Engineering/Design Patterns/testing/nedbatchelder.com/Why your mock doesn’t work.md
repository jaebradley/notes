# [Why your mock doesn’t work](https://nedbatchelder.com/blog/201908/why_your_mock_doesnt_work.html)
* Variables in Python are names that refer to values
* Assigning a second name, like `y = x` means that `y` doesn't refer to `x` but rather that they both refer to the same value
  * If `x` is reassigned, `y` is not affected
```python
# mod.py

val = "original"

def update_val():
    global val
    val = "updated"

# code1.py

from mod import val, update_val

# will print "original"
print(val)
update_val()
# will print "original"
print(val)
```
* `from mod import val` means "import `mod` and then do the assignment `val = mod.val`"
  * After the invocation of `update_val` , the `val` variable in `mod.py` will refer to `updated` but the `val` variable in `code1.py` will still refer to `"original"`
```python
# code2.py

import mod

# will print "original"
print(mod.val)
mod.update_val()
# will print "updated"
print(mod.val)
```
* `import mod` means that the name `mod` refers to the entire `mod` module
* This means that accessing `mod.val` reaches into the mod module, finds the `val` name and accesses `val`'s value
* `update_val` changes the value that `val` refers to

## Applying this to Mocking
* Mocking is a form of assignment as the mock is replacing the actual logical implementation with a test implementation
* However, reassigning a core library function like `os.listdir`  will replace `os.listdir` wherever it is used
* So instead of mocking `os.listdir` , mock `my_implementation.listdir` 
  * While this might seem odd since `listdir` isn't (explicitly) defined in `my_implementation.py` 
  * This means that only the `listdir` reference that `my_implementation.py` refers to will be mocked
* If `my_implementation.py` had `import os` at the top the mock would not have been able to be created properly
