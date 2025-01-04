# [Disjoint Set Union (DSU)/Union-Find - A Complete Guide](https://leetcode.com/discuss/general-discussion/1072418/Disjoint-Set-Union-(DSU\)Union-Find-A-Complete-Guide)

* Sets are disjoint if the intersection of any two sets is `NULL` (i.e. they do not overlap)
* Usually the problem involves several elements with each element in a separate set, initially
* Two types of operations
  * Combine two given sets
  * Evaluate the connectivity of two elements (if they are in the same set, or not)

## Example

* Initial elements of `a`, `b`, `c`,...,`h`, each in their own set
* A "representative" for each set, that serves as the "point of contact" for the set
  * Initially, each element is the representative of their set (of which they are, initially, the only element)
  * Can think of an element's "representative" as the immediate parent of that element
* If set `a` and set `b` are combined
  * And since we have to pick one set to be the parent, let's say that set `a` becomes set `b`'s parent
  * Now `b`'s "representative" is `a`
* Let's say `c` and `d` combine, as well as `e` and `f`, along with `g` and `h`

| Element        | a | b | c | d | e | f | g | h |
|----------------|---|---|---|---|---|---|---|---|
| Representative | a | a | c | c | e | e | g | g |

* Combining `a` and `c` leads to `b` pointing to `a` and `c` pointing to `a` and `d` pointing to `c`

| Element        | a | b | c | d | e | f | g | h |
|----------------|---|---|---|---|---|---|---|---|
| Representative | a | a | a | c | e | e | g | g |

## `find` function

* Idea is a function that will return the representative for a given element
* Implementation is usually something like
  * If the specified element is the representative for the specified element (via a mapping like the `Element` <> `Representative` table above) then return the element
  * If the specified element is _not_ the representative specified element, then call `find` on the immediate reprsentative for the specified element
    * This will continue until the "root" representative is identified
    * So for `d` above, first `c` would be checked, then `a` would be checked
    * Since `a`'s representative is itself, `a` would be returned as `d'`s "root" representative

## `combine` function

* When combining elements, first check to see if both elements have the same representative or not
* If they do have the same representative then they are already in the same set, and they are already "combined"
* Otherwise, pick the representative of the first element to be the representative of the seceond element's representative

## Union by Size

* When combining elements, and their respective sets, a larger and larger "tree" is built
* So whichever representative element has a greater "size" (i.e. the set of elements that point to the representative element) use the representative element with the _smaller_ size as the representative for the "combined" set

## Path Compression

* In the above example, `d` points to `c` which points to `a`, ultimately
* What can be done instead is when traversing or "calculating" the representative for a given node, once the representative is found, set that value for all the traversed elements in the set
* So `d` would point to `a` when looking up the representative of `d`
