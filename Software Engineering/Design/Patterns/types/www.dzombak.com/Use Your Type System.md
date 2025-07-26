# [Use Your Type System](https://www.dzombak.com/blog/2025/07/use-your-type-system/)
* Bugs where a "string" representing a user ID gets used as an account ID
  * Or where a function accepts three integer arguments and someone mixes up the correct order when calling the function
* Solution is to define different types and use them when _representing different things_
* Passing `int` / `string` as-is means slowly losing important context _around what they actually represent_
* Author shows trivial example using a `UserID` and `AccountID` that both extend a `UUID`
  * `DeleteUser` can't be called with `AccountID` so there is "type protection" to avoid logically incorrect operations
* Models should each have their own ID type
* Functions (public _and_ private) should avoid dealing in flots or integers alone
