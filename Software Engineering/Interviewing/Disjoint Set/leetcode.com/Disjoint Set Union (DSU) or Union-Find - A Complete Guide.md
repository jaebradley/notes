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

