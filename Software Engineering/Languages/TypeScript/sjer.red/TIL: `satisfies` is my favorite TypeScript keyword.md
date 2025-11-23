# [TIL: `satisfies` is my favorite TypeScript keyword](https://sjer.red/blog/2024-12-21/)

```typescript
// name is of type "Jerred"
const name = "Jerred";

// person1 is of type { name: string }
const person1 = { name: "Jerred" };

// person2 is of type { readonly name: "Jerred" }
const person2 = { name: "Jerred" } as const;
```
* `person1.name`  is `type` `string` because the object could be mutated to contain any other string
* Can pass `person2` to functions that take `person1`'s but no the inverse
* Annotate variable declaration with the expected type
```typescript
const person1: { name: "Jerred" } = { name: "Jerred" };

// Can also use the `satisfies` keyword
const person1 = { name: "Jerred" } satisfies { name: "Jerred" };
```
* `satisfies` tells TypeScript that the assignment should be at least assignable to the provided type
