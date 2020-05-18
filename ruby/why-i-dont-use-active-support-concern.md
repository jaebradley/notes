# [Why I Don't Use ActiveSupport::Concern](http://blog.coreyhaines.com/2012/12/why-i-dont-use-activesupportconcern.html)

* When pulling apart large AR models, group methods into actual conceptual concerns
  * Move them into modules and mix them into AR class - gives good idea of dependency graph between them
  * Concerns should be orthogonal - if they are dependent on each other, usually indication that there is a helper object waiting to be extracted
* Having a module depend on another module should be treated as a design smell - smell often flags a missing, but needed, service object
* Theoretically [a problem with `ActiveSupport::Concern` where it overrides methods on the class that includes it](https://stackoverflow.com/a/43136697/5225575) whereas a normal module is appended to the method lookup chain (i.e. Ruby checks the class first, and then moves up to modules `include`d in the class starting with the first `include`d module)
  * So an `ActiveSupport::Concern` module will always override a method, even if it's implementation is `include`d in a later module
* `ActiveSupport::Concern` tightly couples the idea of a "concern" to a concrete implementation in `ActiveSupport` - sometimes this abstract "concern" should be represented as a module mixin, sometimes a wrapper-tye class, and sometimes a helper class that requests are delegated to