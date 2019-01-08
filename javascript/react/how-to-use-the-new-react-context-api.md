# [How To Use The New React Context API](https://hackernoon.com/how-to-use-the-new-react-context-api-fce011e7d87)

* A common scenario arises when a component's child's *child* needs one of it's `prop` values
  * This can be solved by passing this `prop` down to it's child (and then down one more level)
    * (can also be solved using `Redux`)
* `React`'s `context` API can also solve this using `Provider`s and `Consumer`s
* The idea is that you create a `Context` object via `React`'s `createContext` method
* This `Context` object has a `Provider` component that takes some `state` `prop`
* Any child component can now reference that `state` using the `Context.Consumer` component

```jsx
const SomeAppContext = React.createContext();

class SomeAppContextProvider extends React.Component {
  constructor() {
    // Note how changeToBaz is used later on to modify the state of the entire wrapped Context
    this.state = {
      foo: 'bar',
      changeToBaz: () => {
        this.setState({
          foo: 'baz',
        })
      }
    };
  }

  render() {
    return (
      <SomeAppContext.Provider value={this.state}>
        { this.props.children }
      </SomeAppContext.Provider>
    )
  }
}

// This will display 'bar'
const SomeChildComponent = () => (
  <SomeAppContext.Consumer>
    { (context) => context.foo }
  </SomeAppContext.Consumer>
)

// This will change the value of foo in the Context state to baz
const ChangeToBazComponent = () => (
  <SomeAppContext.Consumer>
    {
      (context) => (
        <button onClick={context.changeToBaz}>
          Change to baz
        </button>
      )
    }
  </SomeAppContext.Consumer>
)
```
