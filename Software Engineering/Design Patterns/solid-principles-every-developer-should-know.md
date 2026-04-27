# [SOLID Principles Every Developer Should Know](https://blog.bitsrc.io/solid-principles-every-developer-should-know-b3bfa96bb688)

## Single Responsibility Principle

```javascript
class Animal {
  constructor() {}
  getAnimalName() {}
  saveAnimal(a) {}
}
```

* `Animal` has two responsibilities - animal database management and animal property management
* If there's any type of change that affect database management, the classes that make use of `Animal` properties will have to be changed which causes the system to be rigid / have behavioral dependencies
* Instead, create an `Animal` class and an `AnimalDB` class that does the work for saving / fetching `Animal`s
* Separate features if they will change for different reasons

## Open / Closed Principle

```javascript
function AnimalSound(animals) {
  for(int i = 0; i <= a.length; i++) {
    if(a[i].name == 'lion')
        log('roar');
    if(a[i].name == 'mouse')
        log('squeak');
  }
}
```

* `AnimalSound` does not conform to open/closed principle because it cannot be closed against new kinds of animals
  * If a new type of animal was added, this function would have to be updated too
* Instead, add a `makeSound` for each `Animal` class and then in `AnimalSound` as it's looping, call the same `makeSound` instance method

## Interface Segregation Principle

* Make fine-grained interfaces that are client-specific
* Clients should not be forced to depend upon interfaces that they do not use

```javascript
interface IShape {
  drawCircle();
  drawSquare();
  drawRectangle();
}
```

* Anything implementing this interface (like a `Circle` class) would also have to implement the methods `drawSquare` or `drawRectangle` (which doesn't really make sense)
* Instead, interface should just be an `IShape { draw(); }` and then each class implements a `draw` method

## Dependency Inversion Principle

* Dependency should be on abstractions and not concretions
* High-level modules should not depend upon low-level modules - both should depend on abstractions

```javascript
class XMLHttpService extends XMLHttpRequestService {}
class Http {
    constructor(private xmlhttpService: XMLHttpService) { }
    get(url: string , options: any) {
        this.xmlhttpService.request(url,'GET');
    }
    post() {
        this.xmlhttpService.request(url,'POST');
    }
    //...
}
```

* `Http` is forced to depend on `XMLHttpService` class - instead it should care about the interface it depends on
* Thus, it's easy to swap out the specific class used to connect to a network - don't need to know about the type of network connection

```javascript
class Http {
    constructor(private httpConnection: Connection) { }
    get(url: string , options: any) {
        this.httpConnection.request(url,'GET');
    }
    post() {
        this.httpConnection.request(url,'POST');
    }
    //...
}

class NodeHttpService implements Connection {
    request(url: string, opts:any) {
        //...
    }
}
class MockHttpService implements Connection {
    request(url: string, opts:any) {
        //...
    }    
}
```