# [`How to make your code better with intention-revealing names`](https://medium.freecodecamp.org/how-to-make-your-code-better-with-intention-revealing-function-names-6c8b5271693e)

This code

```javascript
function getTodos(users){
  return todos
    .filter(todo => !todo.completed && todo.type === "RE")
    .map(todo => ({
      title : todo.title,
      userName : users[todo.userId].name
    }))
    .sort((todo1, todo2) =>
      todo1.userName.localeCompare(todo2.userName));
}
```

can be refactored and split up like

```javascript
function isTopPriority(todo){
  return !todo.completed && todo.type === "RE";
}
function ascByUserName(todo1, todo2){
  return todo1.userName.localeCompare(todo2.userName);
}

function getTodos(users){
  function toViewModel(todo){
    return {
      title : todo.title,
      userName : users[todo.userId].name
    }
  }
  return todos.filter(isTopPriority)
              .map(toViewModel).sort(ascByUserName);
}
```

* I slightly disagree with the naming of `ascByUserName` - what is this function doing? `ascending` isn't really a verb
  * The function is some type of comparator - maybe `alphabeticalUserNameComparator`?
* `toViewModel` is interesting in that it's using closure to it's advantage
  * I initially had some objects around it creating a new function on each invocation but in the original implementation, that creates a new anonymous function per invocation so I don't think there is that much of a cost difference

* Consider adding names to anonymous functions and / or moving anonymous functions to named function declarations that reveal the intention of the function
  * > Don’t use function names that add no extra value to the existing code. For example, don’t use names like onClick() or myCustomClickEvent(). The name should express what the code does, and a basic name may be used in other places.
  * Adding names helps with debugging and identification in the call stack
