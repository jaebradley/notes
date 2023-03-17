# [React Is All About Composition](https://medium.com/leanjs/react-is-all-about-composition-f9f49dec183c)

* React is a tree of components
  * Parents may or may not know children ahead of time
  * Children don't know about parents
  * Children don't know about siblings
  * Well-defined interface (aka `props`)
* Composition is a concentric circle where each wrapping circle is equivalent to composed parent component
* Using `children` `props` makes the makes composing the component tree much more declarative
  * By composing components through `children` props, don't need to pass down `props` through tree

```jsx
// Don't need to pass the value through Foo and Bar to Baz
<Foo>
  <Bar>
    <Baz value="blahblahblah" />
  </Bar>
</Foo>
```

* HOC ordering performance considerations
  * Outer HOC prop changes will impact inner HOC rendering
* HOCs are not as declarative - in React, component intent is declared in JSX
* Render props can be used to scope logic to particular part of component vs. having to wrap entire component in HOC
  * As the name would suggest, render props have access to props while HOC doesn't have it naturally (like `ownProps`)
