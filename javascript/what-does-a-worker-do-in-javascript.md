# [What Does A Worker Do In JavaScript](https://medium.com/javascript-in-plain-english/what-does-a-worker-do-in-javascript-25b9449f22f1)

* Imagine two `setTimeout`s where the first `setTimeout` does a lot of work and the second `setTimeout` does much less work
  * The second `setTimeout` will only run after the first `setTimeout` finishes
  * Asynchronous functions might be in a different task queue, but they are still executing a single thing at any given time

```javascript
let count = 0;
setTimeout(() => {
  for (let i = 0; i < 10e8; i += 1) {
    count += 1;
  }
  console.log('count is', count);
});
setTimeout(() => {
  console.log('jaebaebae');
});
```

* Web Workers cannot access or manipulate DOM and they live in a completely different thread and does not really interact with main thread
* Can create instances of Web Workers and then post messages to / from worker
  * Worker can listen for messages and execute jobs / actions and / or respond in some way
* Workers are used for things that take a lot of CPU resources like things around graphics
  * Computation doesn't block UI rendering (i.e. stuff on the main thread)