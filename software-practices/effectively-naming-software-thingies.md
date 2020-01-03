# [Effectively Naming Software Thingies](https://medium.com/@rabinovichsagi/effectively-naming-software-thingies-fcea9d78a699)

* Don't make me think - names should not make us raise questions about the meaning of objects and values
* Reveal intention - if a variable is the last updated record, name it `lastUpdatedRecord` and not `record` or `lastRecord`
* Use Solution Domain Names - code is read by programmers so use computer science terms like a `jobQueue`
* Don't abbreviate - use `getWindow` vs. `getWin`
* Pick one word (as a team) for a given abstract concept - always use `fetch` vs. `retrieve` (for example)
* Avoid mental mapping - For example, name loop counters to make it simpler to understand what the variable represents
* Use the same conventions for common operations - `getId()` vs. `id()` vs. `id.get()`
* Use opposites precisely - if you can `open` you should be able to `close`. Same with `start` / `stop`.
* Don't put `I` in front of concrete class to indicate an interface
* Don't add gratuitious context - for example, don't use the abbreviated project name as prefix like `MyProjectMailingAddress`
* Avoid using type in the name like `accountList` - if it gets changed to a `set`, the name loses meaning
* Avoid using `o` or `l` as variable names as they are too similar to `0` and `I`
* Favor readability over brevity
* Avoid easily misspelled names
