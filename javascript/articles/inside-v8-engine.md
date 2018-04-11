# [Inside The V8 Engine](https://blog.sessionstack.com/how-javascript-works-inside-the-v8-engine-5-tips-on-how-to-write-optimized-code-ac089e62b12e)

* V8 engine was created to increase JavaScript execution in browser by just-in-time compiling to machine code instead of using interpreter
* Has multiple threads
  * Main execution thread, which executes code
  * Compile thread, which optimizes code compilation
  * Profiler thread, which tells runtime which methods are time-intensive for optimization purposes
  * Multiple Garbage Collection threads
* First optimization is to inline code by replacing call sites (function calls) with the function body

## Hidden Classes

* Java object properties are determined by fixed object layout that is determined before compilation and cannot be changed at runtime
  * Those properties are stored in a continuous memory buffer and fixed-offset between them
  * This offset can be determined by the property type
* In JavaScript, this offset is impossible to determine because the property type can change at runtime
* Instead, JavaScript interpreters use a hash map to store the location of object property values in memory.
  * This makes retrieving property values more computationally expensive

Let's look at

```javascript
function Point(x, y) {
  this.x = x;
  this.y = y;
}

var p1 = new Point(1, 2);
```

* When the `new Point(1, 2)` invocation happens, a new Hidden Class (`C0`) is created that `p1` references
* This new Hidden Class is empty, since none of the variable initialization has happened (yet)

![c0](https://cdn-images-1.medium.com/max/1600/1*pVnIrMZiB9iAz5sW28AixA.png)

* When `x` gets set, a new Hidden Class (`C1`) is created that has set `x` to an `offset` of `0`, which means that when viewing the point object in memory as a continuous buffer, the first offset will correspond to property `x`.
* A "class transition" is added to `C0` that says that if property `x` is added to a point object, the hidden class should switch from `C0` to `C1`.

![c1](https://cdn-images-1.medium.com/max/1600/1*QsVUE3snZD9abYXccg6Sgw.png)

* Same thing happens when `y` is added
* Property order matters when creating Hidden Classes

```javascript
function Point(x, y) {
    this.x = x;
    this.y = y;
}

var p1 = new Point(1, 2);
p1.a = 5;
p1.b = 6;

var p2 = new Point(3, 4);
p2.b = 7;
p2.a = 8;
```

* Because `p1` adds property `a` first and then adds property `b` while `p2` adds `b` first and then `a` they end up with different transition paths, and thus, different hidden classes
* This is why initializing dynamic properties in the same order is better because these hidden classes can be reused

## Inline Caching

* Inline caching is the idea that repeated calls to the same method tend to occur on the same type of object
* V8 maintains a cache of the type of objects that were passed as a parameter in the future
  * If V8 is able to make good assumptions about the type of object that will be passed to a method, it can bypass the process of figuring out how to access the object's properties, and instead, use the stored information from previous lookups to the object's hidden class
  * This means that after two successful calls to the same method on the same hidden class, V8 won't perform a lookup to determine an offset for that property but will automatically go to the stored offset
  * This also means that objects of the same type that have different hidden classes won't be able to utilize inline caching because their hidden classes have different offsets

## Garbage Collection

* Mark and Sweep
  * "Mark" stops JavaScript execution
  * V8 uses incremental marking
    * Walks part of the heap then resumes execution
    * On next GC stop, continue from previous heap walk stop
    * Short pauses during normal execution
  * Sweep is handled by separate threads
