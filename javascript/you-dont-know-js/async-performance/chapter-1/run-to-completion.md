# [Run To Completion](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch1.md#run-to-completion)

* Difference between `async` (which is about the gap between the execution that will happen now vs. the execution that will happen later) and parallel computation (which is, well, the computation that occurs simultaneously)

* Because JavaScript is single-threaded, the code inside of `foo` will run before any code in another independent function like `bar` will run
