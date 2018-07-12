# [What Is Hoisting?](https://codeburst.io/javascript-what-is-hoisting-dfa84512dd28)

```javascript
cowSays('moo');
function cowSays(sound){
  console.log(sound);
}

// 'moo' is console logged
```

* Hoisting is when function and variable declarations are added to memory during the compile phase

```javascript
console.log(a);
var a = 3;

// undefined
```

**JavaScript only hoists declarations. Initializations are not hoisted**
