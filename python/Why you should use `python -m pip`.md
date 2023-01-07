# [Why you should use `python -m pip`](https://snarky.ca/why-you-should-use-python-m-pip/)

* `python -m pip` executes `pip` using the specified Python interpreter
* `python3.9 -m pip` or `/usr/bin/python3.9 -m pip` means `pip` is executed with the interpreter at the specified location
* Mostly useful when multiple versions of Python are installed on a machine
  * To know which interpreter would be called, would need to introspect the `PATH` variable to see the order in which directories are added to the `PATH`
  * Unless you know when you installed each version of Python, and thus what the last copy of `pip` was written to `/usr/local/bin/pip` then it's unknown what interpreter `pip` will be using for the `pip` command
* Never install stuff into the global Python interpreter
  * May break system if you install incompatible version of a library that the OS relies on
* For installing a tool in isolation, use `pipx`
  * Each tool gets its own virtual environment
* `python -m venv` for creating an environment
