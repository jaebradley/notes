# [How to Use Classes and Sleep at Night](https://medium.com/@dan_abramov/how-to-use-classes-and-sleep-at-night-9af8de78ccb4)

* Downside of classes in JavaScript
  * Obscure prototypal inheritance at core of JS
  * Classes incourage inheritance but you should prefer composition
  * Classes lock you into first bad design you come up with
* Resist making classes your public API
  * If you exmpose them, people will inherit from them in all sorts of ways that make zero sense to you, but that you may break in future
  * Hide classes behind factory functions
* Don't inherit more than once
  * Descendants have too much access to implementation details of every base class in heirarchy
  * Refactoring class hierarchy is really hard
* If you override a method in a derived class, override it completely (don't use `super`)
* Prefer to write React components as pure functions when possible

