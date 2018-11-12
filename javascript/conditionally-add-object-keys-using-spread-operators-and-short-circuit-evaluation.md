# [Conditionally add object keys using spread operators and short circuit evaluation](https://medium.com/@mikeh91/conditionally-adding-keys-to-javascript-objects-using-spread-operators-and-short-circuit-evaluation-acf157488ede)

This pattern can happen quite a bit

```javascript
const parameters = {};

if (values.foo) {
  parameters.foo = values.foo;
}

if (values.bar) {
  parameters.bar = values.bar;
}

return parameters;
```

But it can be one-lined like

```javascript
const parameters = {
  ...(values.foo && { foo: values.foo }),
  ...(values.bar && { bar: values.bar }),
};
```

When `values.foo` is `falsy`, then nothing is gonna get spread.
