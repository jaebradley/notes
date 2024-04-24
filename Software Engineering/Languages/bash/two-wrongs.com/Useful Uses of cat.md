# [Useful Uses of `cat`](https://two-wrongs.com/useful-uses-of-cat)

* Programs are structures as chains of components
* Each component receives data from the previous component, processes the data, and then sends the data to the next program
* If one component in this chain is no longer needed, the code is hard to remove because the output of its predeccor is not compatible with the input requirements of its successor

```bash
# read the file, truncate to the first 500 lines
cat access.log | head -n 500

# combines reading the file and truncating the first 500 lines
head -n 500 access.log
```

* In the above example, in the second example, `head` performs two different responsibilities - it reads the file and then truncates the output
  * If we had to delete the `head` program call, we would need to add a step to read the contents of the `access.log` file
* Having a `cat` preceding the `head` means that if we needed to delete the `head` program call, we wouldn't need to add the additional step
