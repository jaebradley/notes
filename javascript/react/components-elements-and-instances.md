# [Components, Elements, and Instances](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html)

## Typical object-oriented UI system

* `Form` component has a `Button` child component
  * It creates instance of child component and keeps it up-to-date with any new information
  * It's also in charge of creating and removing child component
  * It also creates instances for all of `Button`'s children
  * Makes it hard to decouple since parent's have access to children

## Elements

* React has elements which are immutable objects that describe the UI that needs to be rendered
* These objects have two properties - `type` and `props`
  * `type` can be a string or component
    * If a string, it represents the DOM node with that name
  * `props` is an object that represents the attributes for the UI, like any children or it's color attribute

* These are equivalent

```javascript
{
  type: 'button',
  props: {
    className: 'button-blue',
    children: {
      type: 'b',
      props: {
        children: 'Ok!',
      },
    },
  },
}
```

```html
<button class="button-blue">
  <b>
    Ok!
  </b>
</button>
```

* Note, everything is just objects - not instances

## Components Encapsulate Element Trees

* When React sees an element with a function or class `type` it asks that component what elements it renders
* So it will ask this element what it renders

```javascript
{
  type: Button,
  props: {
    color: "blue",
    children: "Ok!"
  }
}
```

* `Button` will return this element

```javascript
{
  type: "button",
  props: {
    className: "button-blue",
    children: {
      type: "p",
      props: {
        children: "Ok!",
      },
    },
  },
}
```

* React does this until it knows the underlying DOM tag elements for every component on the page
