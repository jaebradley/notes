# Item 6: Eliminate obsolete object references

```java
public class Stack {
  public void push(Object e) {
    ensureCapacity();
    elements[size++] = e;
  }

  public Object pop() {
    if (size == 0) {
      throw new EmptyStackException();
    }

    return elements[--size];
  }

  private void ensureCapacity() {
    if (elements.length == size) {
      elements = Arrays.copyOf(elements, 2 * size + 1);
    }
  }
}
```

* The `Stack` class contains a memory leak where as the stack grows and then shrinks, the objects that were popped off the stack will not be garbage collected, even if the program using the `Stack` has no more references to the objects that were popped off the stack
* This is because the `Stack` contains obsolete references to these objects
  * Obsolete references are references that will never be dereferenced again
* In the `Stack` case, any references outside of the "active portion" (i.e. elements whose index is less than `size`) of the element array are obsolete
  * This is because the underlying array doesn't shrink in size, it only grows. So even though element X is popped from the stack, it still "exists" in the array (i.e. the array has a reference to the `Object`) so the underlying `Object` doesn't get garbage collected.
* If an object reference is unintentionally retained, this is problematic as not only is that object excluded from garbage collection but any objects referenced by that object, etc.
  * So if only a few object references are unintenionally retained, many, many objects may be prevented from being garbage collected
* In the case of the `Stack` class, the solution is to `null` out references once they become obsolete
  * In the `pop` method add `elements[size] = null` after getting the object from the `elements` array
  * This pattern (i.e. nulling out object references) should be the exception rather than the norm
  * The reason that `Stack` is susceptible to this problem is that it manages its own memory
    * The `Stack` manages its own memory in the sense that it has a "storage pool" consisting of the `elements` array (which is an array of object references)
    * The elements in the "active portion" of the array are allocated and the elements in the remainder of the array are free
    * However, the garbage collector has no way of knowing this - to the garbage collector, all object references in the `elements` array are equally valid - only the programmer knows that the inactive portion of the array is unimportant
    * The programmer must communicate this to the garbage collector by manually nulling out array elements as soon as they become part of the inactive portion
* Another common source of memory leaks is caches
  * Once you put an object reference in a cache, it's easy to forgot that it's there and leave it in the cache long after it becomes irrelevant
  * If a cache entry is only relevant as long as there are references to its key outside of the cache, represent the cache as a `WeakHashMap`
    * A `WeakHashMap`-backed cache will automatically remove entries after they become obsolete - in other words, `WeakHashMap` is only useful if the desired lifetime of cache entries is determined by external references to the key, not the value
