# [Functional Programming - How and Why](https://onsclom.bearblog.dev/functional-programming-how-and-why/)

* Program that groups newline-separated numbers, sums these groups, and returns the largest sum amongst these groupings

```
1000
2000
3000

4000

5000
6000

7000
8000
9000

10000
```

## Imperative Solution

```javascript
const input = $("pre").innerText
let runningMax = 0
for (let group of input.split("\n\n")) {
    let runningSum = 0
    for (let num of group.split("\n"))
        runningSum += Number(num)
    runningMax = Math.max(runningMax, runningSum)
}
console.log(runningMax)
```


* Two pieces of state would be kept, the running group sum, and the running maximum group sum found
* These pieces of state would be updated when iterating over each element in the group, and after finishing iterating over a group, respectively

## Functional Solution

```javascript
const input = $("pre").innerText
function sumGroup(group) {
    return group
    	.split("\n")
    	.map(Number)
    	.reduce((a,b)=>a+b)
}
const sortedGroups = input
    .split("\n\n")
    .map(sumGroup)
    .sort((a,b)=>a-b)
console.log(sortedGroups.at(-1))
```

* No more variable mutation, everything is "calculated" via stateless data transformation functions
* Iterative loop statements can be replaced by recursion or higher-order functions

### `range` example

* `range(5)` returns `[1, 2, 3, 4, 5]`

```javascript
// Iterative
function range(end) {
    let nums = []
    for (let i=1; i<=end; i++)
    	nums.push(i)
    return nums
}

// Functional / Recursive (but faces performance issues)
function range(end, cur=[]) {
    if (cur.length >= end) return cur
    return range(end, [...cur, cur.length+1])
}
```

* The functional approach keeps "spreading" / "exploding" the previous array of `n` elements, and then adding the value `n + 1` at the end of the previous array
  * It continues this recursion, until the input array is the target size

## Advantages of Functional Programming

* Iterative logic can intertwine data and behavior (like mutating data associated with a for-loop)
  * Functional logic separates data and behavior - the data is immutable or only manipulated by some stateless transforming logic (like a `map` function)
  * The separation of data and behavior means that functional / declarative code describes _what_ is occurring
  * In the imperative solution, it might have been tempting to name the variables `runningMax` and `runningSum`, `maxGroup` and `sum` instead
    * But the names `sum` and `maxGroup` would be lying as they mutate / change over the course of the program
    * Thus these variable names describe _how_ things are done
  * In the functional solution, the function `sortedGroups` describes _what_ the logic is doing
* In imperative code, it is common to write void functions that mutate state
  * The functional equivalent would be to take the starting state and return some updated state
