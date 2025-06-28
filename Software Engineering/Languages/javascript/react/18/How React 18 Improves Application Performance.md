# [How React 18 Improves Application Performance](https://vercel.com/blog/how-react-18-improves-application-performance)
* JS browser engine executes code in a single-threaded environment (main thread)
* Main thread is responsible for handling other tasks, like user interactions (clicks, keystrokes) alongside network events, timers, animations, layout, repainting
* When a task is being processed, all other tasks must wait
* Any task that takes more than 50 milliseconds is considered a “long task”
* Devices must create a new frame every 16ms (or so) to maintain a smooth visual experience
* Total Blocking Time measures the time betwen First Contentful Paint and Time To Interactive
* Total Blocking Time is the sum of the time it took tasks longer than 50 ms to execute
* Interaction to Next Paint measures the time from a user’s first interaction with the page (clicking a button) to when the itneraction is visible on -screen

## Traditional React Rendering
* Two parts to a visual update in React: render phase and commit phase
* Render phase is a pure computational phase where React elements are reconciled with the existing DOM
* Phase involves creating a new tree of React elements known as the virtual DOM - lightweight in-memory representation of the actual DOM
* React applies the virtual DOM updates calculated during the render phase in the commit phase and updates the actual DOM
* Traditionally, synchronous rendering is an all-or-nothing operation where a component that starts rendering will always finish
* So depending on the complexity of the component, the render phase could take time to complete
* Main thread is blocked during this time, so users trying to interact with the application experience an unresponsive UI until React completes the render phase and commits the result to the DOM
* React 18 introduces a new concurrent renderer that exposes ways to mark certain renders as non-urgent
* Non-urgent renders will yield back to the main thread every 5 milliseconds to see if there are more important tasks to execute instead (like user input or rendering other React components)
* This concurrent renderer is able to to concurrently render multiple versions of the component tree without immediately committing the result

## Transitions
* Mark an update as non-urgent by using the `startTransition` function available via the `useTransition` hook
* Use the `startTransition` function to let React know that we’re okay deferring or interrupting rendering to prioritize more important tasks to keep the current user interface interactive
* Concurrent renderer keeps the result in memory until the React scheduler can commit the result to the DOM

## Suspense
* When a component is suspended, React pauses the rendering of the suspended component and shifts focus to other tasks
* React can reprioritize components based on user interaction
* When a user interacts with a suspended component that is not currently being rendered, React suspends the ongoing render and prioritizes the component that the user is interacting with

## Data Fetching
* React 18 has a cache function that remembers the result of the wrapped function call
* If you call the same function, with the same arguments within the same render pass, it will use the memoized value without the need to execute the function again
* `fetch` calls contain a similar caching mechanism by default without having to use `cache`
