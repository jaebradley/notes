# Item 52: Refer to objects by their interfaces

* If an appropriate interface type exist, then parameters, return values, variables, and fields should all be declared using interface types
  * `List<String> strings = new Vector<String>();` vs. `Vector<String> strings = new Vector<String>();`
* If you want to switch implementation can just change the class name in the constructor vs. changing the variable type
* If the original implementation offers some special functionality not required by the general contract of the interface and the code depended on that functionality, then it is criticial that whatever the replacement class is would also provide that same functionality
  * If code surrounding the first declaration depended on `Vector`'s synchronization policy, then it would be incorrect to substitute `ArrayList` for `Vector` in the declaration
* If an object belongs to a class-based framework, it is preferable to refer to it by the relevant base class, which is typically abstract, than by its implementation class
* If classes that implement an interface but provide extra methods not found on the interface (like `PriorityQueue`), should be referred to by its class only if the program relies on the extra methods
