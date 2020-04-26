# Item 43: Specify An Upper Bound For Gem Dependencies

* When developing an application - best bet is to manage dependencies with Bundler and a Gemfile with an explicit version `gem('money', '5.1.1')`
  * This both maintains an explict version requirement for direct dependencies, and Bundler uses the the `Gemfile.lock` file to maintain dependencis of those gems
  * No gem can get updated without somebody updating a version requirement in the Gemfile
* This is a big strict, so a good middle-ground is to specify a minor version range (`gem('money', '>=5.1.0', '<5.2.0')`)
  * Can also be shortened to using the pessimistic version operator (`~>`)
* When developing a library, having tightly bound versions of dependencies can be restrictive for people using the library
  * If your library depends on `money@4.2.0` and an end user has `money@4.3.0`, they can no longer use the library because their application would require two different version of `money` and that's not allowed
  * So when building a library, you have responsibility to provide flexible range of versions for the dependenncy
    * Lower bound is easy - pick the earliest version of the dependency that works with library
    * Upper bound is trickier since omitting upper bound means that you'll support all future versions of the dependency (which may or may not be true)
      * Maybe supply a reasonable upper bound which can be last known working version of dependency, or increase flexiblility by supporting all versions up to next major release
      * So in previous case where library works with `money@4.2.0` and with `money@5.1.1` you can write the `money` `Gem::Specification` like `gem.add_dependency('money', '>=4.2.0', '< 5.2.0')`
      * Makes gem flexible enough to work with past and future versions of dependency without risking too much breakage
      * Also means, don't have to release new version of gem each time developer of gem releases a new patch