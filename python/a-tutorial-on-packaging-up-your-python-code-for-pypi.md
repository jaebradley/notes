# [A Tutorial On Packaging Up Your Pythong Code For PyPi](https://snarky.ca/a-tutorial-on-python-package-building/)

* "packagingin up" = bundling up some library code for easy distribution (not application code)
* A `wheel` is a specially-formatted zip file where `pip` installs the wheel by unzipping the wheel file and copying all files to the correct location
  * There's no compilation so installation is very fast
  * Pip can cache the wheel for later use
  * A lot of wheels are necessary to cover the platforms & python versions that people use
    * Numpy has 22 different wheels that cover different combinations of Python versions, OS and CPU architectures, etc.
* If a wheel isn't available for a platform, use a "source distribution" (or `sdist`)
  * A `tar.gz` file which contains all files necessary to build a wheel
  * If pip can't find a wheel to work for you, it grabs the `sdist` and builds a custom wheel
* Using `setuptools` means that you'll probably want at least a `setup.py`, `setup.cfg`, and a `MANIFEST.in`
  * `PEP 518` defines a `pyproject.toml` file which lets package maintainers specify what build tools they depend on
    * Allows build tools to store configuration information in the `pyproject.toml` file
    * `setuptools` no longer the defacto / only build tool people use
