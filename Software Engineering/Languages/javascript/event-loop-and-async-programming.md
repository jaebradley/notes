# [Event Loop and Async Programming](https://blog.sessionstack.com/how-javascript-works-event-loop-and-the-rise-of-async-programming-5-ways-to-better-coding-with-2f077c4438b5)

## Event Loop Example

* The JavaScript engine has never done anything more than executing a single chunk of your program at any given moment
* JS Engine doesn't run in isolation - it runs inside a hosting environment like a browser or Node.js
* JS Engine is kind've an on-demand execution environment

![event-loop-setup](https://cdn-images-1.medium.com/max/1600/1*FA9NGxNB6-v1oI2qGEtlRQ.png)

* Event loop has one simple job, to monitor the Call Stack and the Callback Queue
* If the Call Stack is empty, it will take the first event from the queue and will push it to the Call Stack, which effectively runs it
* This iteration is called a **tick** in the Event Loop

Let's see how the following gets executed in the context of the Call Stack, Callback Queue and Event Loop

```javascript
console.log('Hi');
setTimeout(function cb1() {
    console.log('cb1');
}, 5000);
console.log('Bye');
```

### `console.log('Hi')` is added to Call Stack

![step-1](https://cdn-images-1.medium.com/max/1600/1*dvrghQCVQIZOfNC27Jrtlw.png)

### `console.log('Hi')` is executed

![step-2](https://cdn-images-1.medium.com/max/1600/1*yn9Y4PXNP8XTz6mtCAzDZQ.png)

### `console.log('Hi')` is removed from Call Stack

![step-3](https://cdn-images-1.medium.com/max/1600/1*iBedryNbqtixYTKviPC1tA.png)

### `setTimeout(function cb1() { ... })` is added to the Call Stack

![step-4](https://cdn-images-1.medium.com/max/1600/1*HIn-BxIP38X6mF_65snMKg.png)

### `setTimeout(function cb1() { ... })` is executed

Browser creates a timer as part of the Web APIs - it handles the countdown for you

![step-5](https://cdn-images-1.medium.com/max/1600/1*vd3X2O_qRfqaEpW4AfZM4w.png)

### `setTimeout(function cb1() { ... })` is complete and removed from Call Stack

![step-6](https://cdn-images-1.medium.com/max/1600/1*_nYLhoZPKD_HPhpJtQeErA.png)

### `console.log('Bye')` is added to Call Stack

![step-7](https://cdn-images-1.medium.com/max/1600/1*1NAeDnEv6DWFewX_C-L8mg.png)

### `console.log('Bye')` is executed

![step-8](https://cdn-images-1.medium.com/max/1600/1*UwtM7DmK1BmlBOUUYEopGQ.png)

### `console.log('Bye')` is removed from Call Stack

![step-9](https://cdn-images-1.medium.com/max/1600/1*-vHNuJsJVXvqq5dLHPt7cQ.png)

### After at least `5000ms` the timer completes and it pushes the `cb1` callback to the Callback Queue

![step-10](https://cdn-images-1.medium.com/max/1600/1*eOj6NVwGI2N78onh6CuCbA.png)

### Event Loop takes `cb1` from the Callback Queue and pushes it to the Call Stack

![step-11](https://cdn-images-1.medium.com/max/1600/1*jQMQ9BEKPycs2wFC233aNg.png)

### `cb1` is executed and adds `console.log('cb1')` to the Call Stack

![step-12](https://cdn-images-1.medium.com/max/1600/1*jQMQ9BEKPycs2wFC233aNg.png)

### `console.log('cb1')` is executed

![step-13](https://cdn-images-1.medium.com/max/1600/1*lvOtCg75ObmUTOxIS6anEQ.png)

### `console.log('cb1')` is removed from the Call Stack

![step-14](https://cdn-images-1.medium.com/max/1600/1*Jyyot22aRkKMF3LN1bgE-w.png)

### `cb1` is removed from Call Stack

![step-15](https://cdn-images-1.medium.com/max/1600/1*t2Btfb_tBbBxTvyVgKX0Qg.png)

* `setTimeout` doesn't automatically put callback on the event loop queue - it sets up a timer
* When timer expires, callback is placed onto the event loop, and a future tick picks it up and executes it
* `setTimeout(someCallback, 1000);` means that in `1,000ms`, `myCallback` will be added to the event loop queue **and not that it'll be executed in `1,000ms`**

```javascript
console.log('Hi');
setTimeout(function() {
    console.log('callback');
}, 0);
console.log('Bye');
```

Will output

```bash
Hi
Bye
callback
```

even though the wait time is set to `0ms`

## Job Queue

* Queue attached to end of every tick in the event loop queue
* Certain async actions that occur during a tick of the event loop will not cause a whole new event to be added to the event loop queue, but will instead add a Job to the current tick's Job queue
* This means that you can add functionality to be executed later and rest assured it will be executed right after, before anything else.
