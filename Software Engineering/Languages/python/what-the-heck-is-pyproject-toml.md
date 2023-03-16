# [What The Heck Is pyproject.toml](https://snarky.ca/what-the-heck-is-pyproject-toml/)

* Before PEP 518 and `pyproject.toml` there was no way to tell a tool like `pip` what build tools it required in order to build a wheel / sdist
* `setuptools` has a `setup_requires` argument to specify what is necessary to build project but can't read that setting unless you have setuptools installed
  * `virtualenv` installs setuptools by default and `pip` always injects setuptools and wheel when running a `setup.py` file
* A tool like `pip` can read a `pyproject.toml` file and see what build tools are specified and install those in the virtual environment to build your project

```pyproject
[build-system]
requires = ["setuptools >= 40.6.0", "wheel"]
build-backend = "setuptools.build_meta"
```

* This is a `pyproject.toml` file for setuptools users
* No need for a `setup.py` if you have a `pyproject.toml` file and a `setup.cfg` configuration file
  * Still need a `setup.py` shim for editable installs

  ```python
  #!/usr/bin/env python

  import setuptools

  if __name__ == "__main__":
    setuptools.setup()
  ```
