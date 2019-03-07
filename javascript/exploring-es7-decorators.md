# [Exploring ES7 Decorators](https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841)

* Decorators provide is a function that takes another function and extends it's behavior without modifying it

## Class Method Example

```javascript
class Cat {
  meow() {
    return `${this.name} says Meow!`;
  }
}
```

Adding the `meow` function evaluates to roughly

```javascript
Object.defineProperty(Cat.prototype, 'meow', {
  value: specifiedFunction,
  enumerable: false,
  configurable: true,
  writable: true,
});
```

A `@readonly` decorator would look something like

```javascript
function readonly(target, key, descriptor) {
  descriptor.writable = false;
  return descriptor;;
}
```

The `Cat` class would be modified

```javascript
class Cat {
  @readonly
  meow() {
    return `${this.name} says Meow!`;
  }
}
```

such that doing something like

```javascript
const garfield = new Cat();
garfield.meow = function() { console.log('I am Garfield'); }
```

would throw an `Exception`.

**Method decorators have the same signature as `Object.defineProperty`, and execute before the relevant `defineProperty` actually occurs**

## Decorating a class

* Decorator takes the target constructo

## Class Example

```javascript
function superhero(target) {
  target.isSuperhero = true;
  target.power = 'flight';
}

@superhero
class MySuperHero() {};

console.log(MySuperHero.isSuperhero); // true
```

Can build a `@superhero` factory function that takes arguments

```javascript
function superhero(isSuperhero) {
  return function(target) {
    target.isSuperhero = isSuperhero;
  }
}

@superhero(true)
class MySuperHero() {};

console.log(MySuperHero.isSuperhero); // true

@superhero(false)
class MySuperHero() {};

console.log(MySuperHero.isSuperhero); // false
```
