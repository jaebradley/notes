# [Concurrency](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch1.md#concurrency)

* Imagine website with status updates that progressively loads as user scrolls
* This means that `onscroll` events will need to fire Ajax requests
* These Ajax responses need to be processed and rendered onto the page

* The `onscroll` events might look something like

```text
onscroll, request 1
onscroll, request 2
onscroll, request 3
onscroll, request 4
onscroll, request 5
onscroll, request 6
onscroll, request 7
```

* The `response` events might look something like

```text
response 1
response 2
response 3
response 4
response 5
response 6
response 7
```

* Because `response` events might come back at the same time as new `onscroll` events get fired, these events are going to be interleaved
* However, JavaScript can only handle one event at a time as it's single-threaded, so either the `response` is going to get processed or the `onscroll` is going to get fired, but obviously both won't happen simultaneously
* Here's an example of these events being interleaved
* Note how the responses can get returned out of order (`response 6` is before `response 5`)

```text
onscroll, request 1   <--- Process 1 starts
onscroll, request 2
response 1            <--- Process 2 starts
onscroll, request 3
response 2
response 3
onscroll, request 4
onscroll, request 5
onscroll, request 6
response 4
onscroll, request 7   <--- Process 1 finishes
response 6
response 5
response 7            <--- Process 2 finishes
```
