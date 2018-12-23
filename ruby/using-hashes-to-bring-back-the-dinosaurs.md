# [Access Ruby Hash Values with Fallbacks for Missing Data](https://robots.thoughtbot.com/using-hashes-to-bring-back-the-dinosaurs)

* Obviously can access Ruby hash values with the `[]` method like `some_mapping[:some_key]`
* Can deal with `nil` doing `some_mapping[:some_key] || 'some fallback'`
  * However, doesn't deal with the case where `:some_key` is `false` (as that will trigger fallback logic)
* `fetch` works like `[]` but takes a fallback value as the second parameter when the key does not exist or if it is `nil`
  * `fetch` will *always* evaluate the second argument, even if it's not used
  * So if second argument is an expensive computation, want to only run it conditionally
  * `fetch` allows the ability to specify an optional block that will conditionally run
    * `params.fetch(:foo) { some_expensive_operation }`
