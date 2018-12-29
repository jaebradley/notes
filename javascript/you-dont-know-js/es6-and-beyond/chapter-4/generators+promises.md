# [Generators + Promises](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch4.md#generators--promises)

```javascript
// Promises
step1()
  .then(
    step2,
    step1Failed
  )
  .then(
    function step3(msg) {
      return Promise.all( [
        step3a( msg ),
        step3b( msg ),
        step3c( msg )
      ] )
    }
  )
  .then(step4);

// ...Converted to Generators
try {
  var result = yield step1();
} catch (e) {
  result = yield step1Failed(e);
}

result = yield step2();

result = yield Promise.all([
  step3a(result),
  step3b(result),
  step3c(result),
]);

yield step4(anotherResult);
```

* Generator can yield a `Promise` and then a runner function can be specified that resumes execution of the function when the yielded `Promise` completes (or `throw`s an error)

```javascript
// Example runner

function run(gen) {
  // Convert args to an actual array of args
  var args = [].slice.call( arguments, 1), it;

  // Initialize generator in current context
  it = gen.apply( this, args );

  // Return Promise when generator completes
  return Promise.resolve()
    .then( function handleNext(value){
      // Run to next generator value
      // Passing back value to iterator
      var next = it.next( value );

      // return Promisified IIFE result
      return (function handleResult(next){
        // If iterator is finished return it's value
        if (next.done) {
          return next.value;
        }
        else {
          // Wrap the iterator value in a Promise.resolve
          return Promise.resolve( next.value )
            .then(
              // If the value is successfully fulfilled, continue iterating
              handleNext,
              // If value is rejected, throw the error in the iterator
              function handleErr(err) {
                return Promise.resolve(
                  it.throw( err )
                )
                .then( handleResult );
              }
            );
        }
      })( next );
    } );
}
```
