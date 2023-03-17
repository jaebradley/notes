# [Writing custom React hooks for GraphQL](https://medium.com/open-graphql/react-hooks-for-graphql-3fa8ebdd6c62)

* `useEffect` is similar to `componentDidMount` and `componentDidUpdate`
* `useReducer` is basically how Redux reducers work
  * Takes `(state, action) => newState` and returns current state

## Queries

* Two ways to query
  * Query on component mount
  * Query on some event (like a button press)
* Querying on component mount means that need to use `useEffect` + `useState` to update internal `todos` list (or whatever internal state to update)

```javascript
// On component load
// Hook code
import { useEffect, useState } from 'react';

const query = "Some query";

export default function queryTodos() {
  // Set todos in state
  // Default to empty array
  const [
    todos,
    setTodos,
  ] = useState([]);
  
  // Make API call and update todos
  useEffect(async () => {
    const todos = await GraphQL.API(query);
    setTodos(todos);
  });

  return todos;
};

// Component code
import queryTodos from './queryTodos;
export default function Todos() {
  return (
    <div>
     { todos.map((todo, i) => <p key={i}>{ todo.name }</p>) }
    </div>
  );
}
``` 

```javascript
// On manual call
// Hook code

import { useState } from 'react';

const query = "Some query";

export default function queryTodos() {
  // Set todos in state
  // Default to empty array
  const [
    todos,
    setTodos,
  ] = useState([]);

  async function query() {
    const todos = await GraphQL.API(query);
    setTodos(todos);
  };

  return [todos, query];
}

// Component code
import queryTodos from './queryTodos';
export default function Todos() {
  const [todos, query] = queryTodos();

  return (
    <div>
      <button onClick={() => query()}
      { todos.map((todo, i) => <p key={i}>{ todo.name }</p>) }
    </div>
  );
}
```

* In the second case, notice how the `async` fetch is passed as an argument from the hook that is called in the `onClick`

## Subscriptions

* Two uses of `useEffect`
  * First is to query for initial set of todos on mount
  * Second is to register subscription that will listen for new todos and add them to list
* Use reducer to handle todos state so that subscription only fires on load vs. on state change
  * Basically, unsure if there's a way to guarantee a single registration on component load _without_ using reducer

```javascript
function todosReducer(state, action) {
  switch (action.type) {
    case "SET":
      return { todos: action.payload };
    case "UPDATE":
      return { todos: [...state.todos, action.payload] };
    default:
      return state;
  }
}

export function useSubscription() {
  const [state, dispatch] = useReducer(todosReducer, initialState);
  
  useEffect(async () => {
    const todos = await GraphQL.API(query);
    dispatch({ type: "SET", payload: todos });
  });

  useEffect(async () => {
    const subscriber = GraphQL.API(subscription).subscribe({
      next: data => dispatch({ type: "UPDATE", payload: data }),
    });
    
    // Unsubscribe subscriber
    return () => subscriber.unsubscribe();
  });
  
  return state.todos;
}
```
