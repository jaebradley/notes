# Recursion

* Recursion terminates
* Recursive code is generally easier to write and understand than iterative code
* Recursion is most useful for tasks that can be defined in terms of similar subtasks
  * Sort, search, traversal often have simple recursive solutions
* Base case is when recursive function encounters a subtask where it does not need to call itself
* _Ecursive_ case is when recursive function calls itself
* Every recursive function can be written

```bash
if (some_base_case_condition) {
  return some_base_case_value;
} else if (some_other_base_case_condition) {
  return some_other_base_case_value;
}

return recursive_method_call(current_value);
```

* Factorial is a good example

```bash
if n == 0
  return 1
else if n == 1
  return 1
else
  n * factorial(n - 1);
```

* Each recursive call **makes** a copy of the method's variables in memory until the call terminates - this results in added space on the stack frame

## Examples

* Tower of Hanoi
  * Move the top `n-1` disks from `Source` to `Auxiliary`
  * Move the `nth` disk from `Source` to `Destination`
  * Move the `n-1` disks from `Auxiliary` to `Destination`
    * Moving these disks can be thought of as a very similar problem to the original problem of moving the top `n` disks
