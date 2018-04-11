# [How Does JavaScript Actually Work](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)

* V8 engine consists of `Memory Heap` and `Call Stack`
  * `Memory Heap` - where memory allocation happens
  * `Call Stack` - the state of what code needs to execute
* JS is single-threaded
  * Single call stack
  * Only does one thing at a time
* `Call Stack`
  * Step into function, put it on top of the stack
  * Return from a function, pop off top of stack

So for

```javascript
function multiply(x, y) {
    return x * y;
}
function printSquare(x) {
    var s = multiply(x, x);
    console.log(s);
}
printSquare(5);
```

the Call Stack will look something like

![call-stack](https://cdn-images-1.medium.com/max/1600/1*Yp1KOt_UJ47HChmS9y7KXw.png)

* Stack traces are just state of Call Stack when exception occurs

![stack-overflow](https://cdn-images-1.medium.com/max/1600/1*AycFMDy9tlDmNoc5LXd9-g.png)

* Long running function calls block browser, which is why async callbacks exist
