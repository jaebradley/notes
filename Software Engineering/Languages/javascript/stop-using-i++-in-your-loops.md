# [Stop Using i++ In Your Loops](https://medium.com/better-programming/stop-using-i-in-your-loops-1f906520d548)

* `i++` is called post-increment and is often implemented like
  * `i++` increments the value of `i` but returns the original value of `i`

```javascript
let j = i;
i = i + 1;
return j;
```

* This means that `i++` uses unnecessary memory as the old value of `i` is generally unused and discarded
* `++i` is called pre-increment and is implemented like

```javascript
i = i + 1;
return i;
```

* Can make argument that compiler can detect primitive values and optimize that line but unless previous value needs to be used, `++i` is generally just as good (if not better) than `i++`
