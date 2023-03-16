# [React Context API - A Replacement for Redux?](https://blog.bitsrc.io/react-context-api-a-replacement-for-redux-6e20790492b3)

* Context API provides a way of passing data to a component without having to pass the data down through props at every level
* Context API
  * Create both a `Provider` and `Consumer` component using `React.createContext`
    * `createContext` takes default values as well
  * `Provider` component is used to accept values
  * `Consumer` component is used below `Provider` and accepts a `children` `prop`

```javascript
// Example Provider wrapper
const DEFAULT_STATE = {
  someId: 1,
};

// Export Context to be used later by Consumer
export const Context = React.createContext(DEFAULT_STATE);

export default class CustomProvider extends React.Component {
  state = {
    someId: 1,
  };

  handleIdChange = id => this.setState({ someId: id });

  render() {
    const {
      children
    } = this.props;

    return (
      <Context.Provider
        value={{
          ...this.state,
          onIdChange: this.handleIdChange,
        }}
      >
        { children }
      </Context.Provider>
    )
  }
}
```

```javascript
// Example Consumer wrapper
// Uses Context instance from Provider component
// Passes values down via render props
// Wraps child components with any of the Consumer's values

import {
  Context,
} from './Provider';

export default function Consumer({ children }) {
  return (
    <Context.Consumer>
      {
        ({
          someId,
          onIdChange,
        }) => (
          React.Children.map(
            children,
            child => React.cloneElement(
              child,
              {
                id: someId,
                onIdChange,
              },
            ),
          )
        )
      }
    </Context.Consumer>
  )
}
```

* Use `Context` API when all you want is simple state management
  * `Redux` when two different components in app need same data
* Current `Context` API only allows a single `Consumer` per `Provider`
