# [UI Algorithms: A Tiny Undo Stack](https://blog.julik.nl/2025/03/a-tiny-undo-stack)
* “undo/redo this action” stack can be implemented using pointers / index
* However, author finds issues with using indices + arrays, like indexing into a non-existent index and having to do `undefined` checks
  * Bugs when calling `.slice` and `.splice`
* Author was looking for an alternative that didn’t involve indexing
* Two stacks: one for undo and one for redo
* Benefit is that no indices or pointers are involved - simply move items from one stack to another

## The pass-by-reference problem
* JS is pass-by-reference for pretty much all of its types
* Closure that encapsulates variable `x` will use whatever the current value of `x` is
* To “clone” a value, `structuredClone` is used
