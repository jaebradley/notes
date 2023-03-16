# [Publishing a Package on PyPi](https://blog.jetbrains.com/pycharm/2017/05/how-to-publish-your-package-on-pypi/)

## Directory Structure

* Root
  * `src`
    * `__init__.py`
    * `some_code.py`
  * `setup.py`
  * `setup.cfg`
  * `README.rst`
  * `LICENSE.txt`
  * `.gitignore`
  * `requirements.txt`

## Example `setup.py`

```python
from setuptools import setup, find_packages

setup(
    name='my_package',
    description='my_description',
    author='My Name',
    author_email='me@gmail.com',
    url='https://github.com/me/my_package',
    version='0.0.1',
    packages=find_packages(),
    include_package_data=True,
    install_requires=[
        'all==0.0.1',
        'these==0.0.2',
        'packages==0.0.3'
    ],
    keywords=['some_keyword'],
    classifiers=['some_classifier']
)
```

## Example `setup.cfg`

```
[metadata]
description-file = README.rst
```

## Example `.pypirc` File

If you do not have a `PyPi` account, go to the `PyPi` website to create an
account and get authentication information.

Once you have your authentication information, you need to create a `.pypirc`
file to store your authentication information locally.

Generally, the `.pypirc` file is found at `~/.pypirc` and looks like

```
[distutils]
index-servers =
  pypi
  pypitest

[pypi]
repository=https://upload.pypi.org/legacy/
username=my_username
password=my_password

[pypitest]
repository=https://test.pypi.org/legacy/
username=my_test_username
password=my_test_password
```

## Commands to Upload Package

```bash
# Navigate to project root directory

# Build source package distribution
python setup.py sdist

# Upload the newly created distribution with twine
# If you have not installed twine yet, do that first
twine upload dist/my_newly_created_distribution.tar.gz
```

## Bonus: Example `travis.yml`

```yaml
language: python
python:
  - "3.6"
# command to install dependencies
install: "pip install -r requirements.txt"
# command to run tests
script:
  - nosetests --with-coverage # Add me to run nose with coverage
after_success:
  - coveralls
```
