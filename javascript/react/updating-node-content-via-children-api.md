# Updating Node Content via React.Children API

Let's say you wanted to add a `jae` prefix to the beginning of all the string content in a React component (I mean, sounds like a great idea).

One way to achieve this is to use a higher-order component and [the `React.Children` API](https://reactjs.org/docs/react-api.html#reactchildren) to wrap the React elements that need to be prefixed.

For example, this `Translation` component

```javascript
class Translation extends Component {
  constructor(props) {
    super(props)
  }

  translateContent(children) {
    return React.Children.map(children, (childNode) => {
      if (typeof childNode === 'string') {
        return `jae ${childNode}`;
      }

      if (typeof childNode.props.children === 'string') {
        return React.cloneElement(childNode, childNode.props, `jae ${childNode.props.children}`);
      }

      return React.cloneElement(childNode, childNode.props, this.translateContent(childNode.props.children));
    })
  }

  render() {
    return (
      <div>
        { this.translateContent(this.props.children) }
      </div>
    );
  }
};
```

will iterate through all it's children, replacing any `string` child nodes (i.e. `string` content) with `jae ${content}`.

It can be used like

```javascript
render() {
  return (
    <div>
      <Hello name={this.state.name} />
      <p>
        Start editing to see some magic happen :)
      </p>
      <Translation>
        <div>Foo</div>
        <div>Bar
          <div></div>
        </div>
        <div className="baz">Baz
          <div className="fuck">Fuck
            <div className="you">You</div>
          </div>
        </div>
      </Translation>
    </div>
  );
}
```

And will output

![alt-text](https://imgur.com/TIvdUEQ.png)

Here's [the full `StackBlitz` example](https://stackblitz.com/edit/jae-hoc-translation-example?file=index.js).

## [`cloneElement`](https://reactjs.org/docs/react-api.html#cloneelement)

```javascript
React.cloneElement(
  element,
  [props],
  [...children]
)
```

> Clone and return a new React element using `element` as starting point - resulting element will have original element's props with new props merged in shallowly.
> New children will replace existing children.
> `key` and `ref` from original element will be preserved, as well as children with `ref`s

* [Another `StackBlitz` example that reverses strings](https://stackblitz.com/edit/string-reverser-hoc?file=Reverser.js)
