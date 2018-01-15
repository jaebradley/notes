# [Everything You Wanted To Know About `package-lock.json` But Were Too Afraid To Ask](https://medium.com/@Quigley_Ja/everything-you-wanted-to-know-about-package-lock-json-b81911aa8ab8)

* `npm@5` will automatically generate a `package-lock.json` file
* Should commit `package-lock.json` to version control

## Why `package-lock.json` is valuable

* Let's say you have a project that relies on `express@^4.15.4`
* On your local machine, you have downloaded `express@4.15.4`
* However, a new version of `express` (`4.15.5`) was just published and if somebody checks out your project and installs the dependencies they'll have the new version locally
* This is problematic if `express@4.15.5` has bugs or has some major behavioral differences relative to `express@4.15.4`
* `package-lock.json` specifies a version, location, and integrity hash for every module and dependency
  * This means that every install should produce the same result every single time

## Manual edits to `package.json`

* `Dependency A@1.1.0` is in `package.json` and `package-lock.json`
* If the dependency is manually edited in `package.json` to be `Dependency A@1.1.1` and `npm install` is executed, the version of `Dependency A` is `1.1.0` and _not_ `1.1.1`.

## Module exists in `package.json` but not in `package-lock.json`

* Since module does not exist in `package-lock.json` it is not installed