# [In-Depth Explanation of State and Props Update in React](https://medium.com/react-in-depth/in-depth-explanation-of-state-and-props-update-in-react-51ab94563311)

```jsx
class ClickCounter extends React.Component {
    constructor(props) {
        super(props);
        this.state = {count: 0};
        this.handleClick = this.handleClick.bind(this);
    }

    handleClick() {
        this.setState((state) => {
            return {count: state.count + 1};
        });
    }

    componentDidUpdate() {}

    render() {
        return [
            <button key="1" onClick={this.handleClick}>Update counter</button>,
            <span key="2">{this.state.count}</span>
        ]
    }
}
```

* Click on button and state update occurs
* When updates are queued, they are added to the queue of updates to process on a Fiber node
  * `updateQueue` property in `ClickCounter` component definition
  * `firstUpdate` linked list (`next.payload`) values is first update to be processed during `render` phase
* After click, React enters `render` phase
* It traverses from topmost `HostRoot` Fiber node and bails out of already processed Fiber nodes until it finds node with unfinished work (in this case, the `ClickCounter` Fiber node)
* Work done on the `ClickCounter` Fiber node is stored in a cloned copy stored on the `alternate` field
  * If alternate node is not created, React creates a copy in the function `createWorkInProgress`

## `beginWork`

* `beginWork` is executed for every Fiber node in a tree, so good place to put breakpoint to debug `render` phase
* `switch` statement that determines the type of work that needs to be done for a Fiber node by the tag
  * Also, executes the respective function to perform the work
  * `ClickCounter` is a class component, so `updateClassComponent` is executed
* `updateClassComponent` will construct, mount, or update the component
* In this case, since class component instance already exists, enter `updateClassInstance`
* `updateClassInstance` calls
  * `componentWillReceiveProps` (to be deprecated)
  * processes `updateQueue` and generates new state
  * call `getDerivedStateFromProps` with new state and get results
  * call `shouldComponentUpdate` - if `false` skip rendering, else proceed
  * `componentWillUpdate` (to be deprecated)
  * add effect to trigger `componentDidUpdate` lifecycle method later (executed in `commit` phase)
  * update `state` and `props` on component instance (needs to be done before `render` method is called)

```javascript
{
    effectTag: 0,
    elementType: class ClickCounter,
    firstEffect: null,
    memoizedState: {count: 0},
    type: class ClickCounter,
    stateNode: {
        state: {count: 0}
    },
    updateQueue: {
        baseState: {count: 0},
        firstUpdate: {
            next: {
                payload: (state, props) => {…}
            }
        },
        ...
    }
}
```

to...

```javascript
{
    effectTag: 4,
    elementType: class ClickCounter,
    firstEffect: null,
    memoizedState: {count: 1},
    type: class ClickCounter,
    stateNode: {
        state: {count: 1}
    },
    updateQueue: {
        baseState: {count: 1},
        firstUpdate: null,
        ...
    }
}
```

* `count` in `memoizedState`, `state`, and `baseState` (in `updateQueue`) has changed to `1`
* `updateQueue` has changed where `firstUpdate` is `null`
* `effectTag` is no longer `0` but `4` (which in binary represents `100`, which means that the third bit is set, which is the bit for `Update` side-effect tag)

### `finishClassComponent`

* React calls `render` method on component instance and applies diffing algorithm to component children
  * As React goes through child reconciliation process, it creates or updates Fiber nodes for child React elements returned by `render` method
  * `finishClassComponent` returns reference to first child - will be used later in work loop
  * React updates props on children as part of work performed by parent
    * Props differ between Fiber node and returned React element
    * `createWorkInProgress` that creates alternate Fiber nodes - React will copy updated properties from React element to the Fiber node

```javascript
// Fiber node for span before React child reconciliation for ClickCounter fiber
{
    stateNode: new HTMLSpanElement,
    type: "span",
    key: "2",
    // When React is finished reconciliation, pendingProps will be updated to { children: 1 }
    // And then it will update memoizedProps and add effects to update DOM
    memoizedProps: {children: 0},
    pendingProps: {children: 0},
    ...
}

// Render method returns...
{
    $$typeof: Symbol(react.element)
    key: "2"
    props: {children: 1}
    ref: null
    type: "span"
}

// After reconciliation, Fiber node is
{
    stateNode: new HTMLSpanElement,
    type: "span",
    key: "2",
    memoizedProps: {children: 0},
    pendingProps: {children: 1},
    ...
}
```

## `span` Fiber Node Work

* React updates `pendingProps` to match `memoizedProps`
* `updateHostComponent` is called where it
  * Prepares DOM updates
  * adds them to `updateQueue` to the `span` fiber
  * adds the effect to update the DOM
* In this case, `effectTag` changes from `0` to `4` (`Update` side-effect tag)
* `updateQueue` field holds payload used for update

## Effects list

* `span` node and `ClickCounter` component have side-effects
* React adds links to `span` Fiber node to `firstEffect` reference in `HostFiber`
  * Effects list is built in `completeUnitOfWork`
  * Child effects are executed before parent effects
  * `span` Fiber node will refer it's `nextEffect` to `ClickCounter`
* `commitRoot` function is used to apply effects and consists of three subroutines

```javascript
function commitRoot(root, finishedWork) {
    commitBeforeMutationLifecycles()
    commitAllHostEffects();
    root.current = finishedWork;
    commitAllLifeCycles();
}
```

* Each subroutine implements loop that iterates over effects and checks the types of effects, and applies any matching effects
* `commitAllHostEffects` is where React will change text on `span` from `0` to `1`
  * Basically, a `switch` on the `effectTag` that applies `updateDOMProperties` using the `updateQueue` payload
* After all DOM updates have been applied, React reassigns the `current` tree property of `HostRoot`to the `finishedWork` "alternate" tree
* `commitAllLifecycles` will call `componentDidUpdate`
