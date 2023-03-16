# [Introducing JavaScript ES6 Proxies](https://hackernoon.com/introducing-javascript-es6-proxies-1327419ab413)

* Proxies are basically ways to override an object's default behavior
* There are three major parts associated with Proxies
  * target object - this is the object that will have some default behavior overridden
  * handler - this is an object that contains the "traps" when various actions are taken on the target object
  * A trap is some kind of default behavior override

```javascript
let handler = {
  get: function(target, name) {
    return name in target ? target[name] : 'key does not exist';
  },
};
let target = {
  reason: 'reason',
  code: 'code',
};
let p = new Proxy(target, handler);

console.log(p.target.reason); // reason
// The following will not log undefined - instead it defaults to behavior defined in handler
console.log(p.target.foobar); // key does not exist
```

* There are a bunch of other traps, but can also use `set` trap like

```javascript
let validator = {
  set: function(obj, prop, value) {
    if (value > 200) {
      throw new RangeError('cannot set value over 200');
    }
    obj[prop] = value;

    return true; // indicate success
  },
};

let proxiedPerson = new Proxy({}, validator);

proxiedPerson.age = 400; // will throw an exception
```
  
