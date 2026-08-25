# [Parse, Don't Validate — In a Language That Doesn't Want You To](https://cekrem.github.io/posts/parse-dont-validate-typescript/)
* Parsers say "give me a blob and I'll give you back a more precise type or tell you why I can't"
* Once a string has been parsed into an `EmailAddress`, the rest of the program never has to worry about whether the string is a valid `EmailAddress` or not
* TypeScript example: a "parsed" `User` where the `email` is a `string` and `age` is a `number`, instead of `EmailAddress` and `Age` values that have been validated and where the types themselves serve as a "proof"
  * TypeScript example also `throws` an `Error` when the user is invalid
* TypeScript is structurally typed, which means that two types with the same shape are the same type
  * There's no `type EmailAddress = String` where the `type` declaration produces a genuinely distinct type

```typescript
/**
Example of "branding" / "tagging" or "nominal typing via intersection"
**/
declare const EmailBrand: unique symbol;
declare const AgeBrand: unique symbol;

type Email = string & { readonly [EmailBrand]: true };
type Age = number & { readonly [AgeBrand]: true };
```

* There's no "brand" field at runtime, it is a type-level marker that makes `Email` and `string` incompatible at compile time
  * The `Email` brand is still assignable to `string`
* The author proposes `parse*` functions that are the sole place for generating type `X` from a `string`
* Author also likes `ok` / `error` instead of boolean discriminants because boolean discriminants look tidy until somebody adds a third case
* In contrast to the initial validator / parser, the failure mode is an exception which is "invisible" to the type system whereas the parser's signature including a failure mode as part of the signature is a complete view of everything that can happen - there is no `throw` behavior that is hiding in the call stack / implementation
* Author distinguishes between the "raw blob" that is received and then the "trusted" data structure that is parsed from that raw blob
  * Author makes this distinction by representing both an `UnvalidatedX` and a `ValidX` (like `UnvalidatedUser` and `ValidUser`)

```typescript
type UnvalidatedUser = {
  id: unknown;
  email: unknown;
  age: unknown;
};

type ValidUser = {
  readonly id: UserId;
  readonly email: Email;
  readonly age: Age;
};
```

* Every domain primitive should be typed - a `UserId` should not be passed where an `OrderId` is expected
* Make the type system carry the proof, not your memory
  * Every time you check something and don't encode the result in a type, you're asking your future self to remember
