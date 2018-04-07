# How Babel Transpiles Default Import vs. Require

I was writing [a library for `GitHub` languages](https://github.com/jaebradley/github-languages-client) (as one does), using Babel to transpile my `ES2015+` library code to `ES5`.

When testing my published library out in the wild, it seemed that when I `default` `export`ed my client, it seemed to work as expected when importing like

```javascript
import Client from 'github-languages-client';
```

However, when I import using `require`...

```javascript
const Client = require('github-languages-client');
```

I have to actually `require` like this

```javascript
const Client = require('github-languages-client').default;
```

to access the actual client methods.

This is due to how Babel transpiles the `default export` and  `import` vs. how `require` works.

[This entire note is based on this `StackOverflow` answer which probably explains what's happening much more accurately, but writing stuff down helps me remember things so here goes.](https://stackoverflow.com/a/42475844/5225575)

When Babel transpiles `default export` it actually assigns the object to export to the `default` property on the `exports` object.

So `default export Jae` will actually be equal to `exports.default = Jae`.

Now for things that are exported as objects, like `export { Jae }`, what ends up happening is that Babel transpiles the `export` like `exports.Jae = Jae`.

The reason why Babel doesn't stumble when `import`ing objects with this `default` property is that it actually "smartly" checks whether or not the exported object is using `ES2015` syntax. If not, it wraps the exported object in an object with a `default` property that is assigned to the exported object.

```javascript
return obj && obj.__esModule ? obj : { default: obj };
```

**TL;DR**: Use https://github.com/59naga/babel-plugin-add-module-exports
