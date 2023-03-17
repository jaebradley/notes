# [Understand Go pointers in less than 800 words or your money back](https://dave.cheney.net/2017/04/26/understand-go-pointers-in-less-than-800-words-or-your-money-back)

* A pointer is a value which points to the memory address of another value
* Computer memory (RAM) can be thought of as a sequence of boxes
  * Each box is labeled with a unique number, incrementing sequentially - this number is the address of memory
  * Each cell holds a value
* Retrieve value in memory address `200`, multiply it by `3` and deposit result in memory address `201`
  * Use variable as an alphanumeric pseudonym for a memory location (a label / nickname)
  * So previous expression is now `a = 6; b = a * 3`
* pointers point to memory address of another variable

```go
func main() {
  a := 200
  b := &a
  *b++
  fmt.Println(a)
}
```

* `b := &a` means that assign a variable, `b`, and assign it the memory address of `a`
* `*b++` means to "dereference" `b` (i.e. follow the pointer back to the value at the memory address it's pointing to) and then increment that value and store the new value in `a`
* `a` should now be `201`

