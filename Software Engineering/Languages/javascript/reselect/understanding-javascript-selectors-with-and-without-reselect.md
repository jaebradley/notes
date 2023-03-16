# [Understanding JavaScript Selectors With and Without Reselect](https://medium.com/@pearlmcphee/selectors-react-redux-reselect-9ab984688dd4)

* Selectors do two things
  * Have knowledge of where to get particular subset of data from
  * And then return this data

```javascript
const getBelts = (state) => state.items.belt
```

* Reselect memoizes results
* It has a `createSelector` function that takes many arguments
  * All except the last argument are one selector functions
  * The last function is a transformer function that takes the result of each selector function as it's arguments

```javascript
import {
  createSelector,
} from 'reselect';

const itemsSelector = state => state.shop.items;

const itemsTotal = createSelector(
  itemsSelector,
  items => items.reduce((sum, item) => sum + item.value, 0),
);
```

* You can use these selectors in components in a `mapStateToProps`, for example
* Selectors can take both `state` and `props` as arguments
* However, if component uses selector with props is in multiple places, memoization error might occur because selectors will be shared across all component instances
  * This means that selector cache will recompute an unnecessary amount of times wince inputs would be changing for each component
  * Need to keep component's selector instance "private"
  * if `mapStateToProps` is a function it will be used to create an individual `mapStateToProps` for each instance of the container

```jsx
const MyComponent = () => (
  <div>
    <SomeOtherComponent selectorProp={'foo'} />
    <SomeOtherComponent selectorProp={'bar'} />
    <SomeOtherComponent selectorProp={'baz'} />
  </div>
)
```

* Create a `mapStateToProps` function
  * Create function that returns a selector
    * Create a `someSelector`, then create a `createSomeSelector` function that returns `createSelector(someSelector, someTransformFunction)`
  * In component, create a `createMapStateToProps` function that returns a `mapStateToProps` object
    * `createMapStateToProps` uses the `createSomeSelector` method previously defined to create a new copy of the selector
    * This will create a private `mapStateToProps` function for each instance of the component
  * Pass `createMapStateToProps` to `connect`
