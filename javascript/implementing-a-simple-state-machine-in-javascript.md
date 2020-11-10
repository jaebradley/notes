# [Implementing A Simple State Machine In JavaScript](https://kentcdodds.com/blog/implementing-a-simple-state-machine-library-in-javascript)

## General State Machine Principles

* One state is defined as the initial state - when a machine starts to execute it automatically enters this state
* Each state can define actions that occur when a machine enters or exits that state - these usually have side effects
* A transition defines how a machine would react to an event by exiting one state and entering another

## Building a "toggle" state machine

* Set an initial state (so this could be "off")
* Allow user to call functions that will be called on entering or exiting a given state
  * `onEnter` or `onExit` of states
* Each state definition will have a `transitions` property where the "on" state will transition to the "off" state and vice-versa
  * This "event" will be called the "switch" event and will be represented by the `switch` property on the `transitions` property / object
* So when the `machine` is in the "off" state calling `machine.transition(state, 'switch')` should transition from "off" state to "on" state
* When machine is transitioning states, may want specific actions - this is so there would be side-effects that only happen when transitioning from state A from specific state B and not from some other state C

```javascript
function createMachine(stateMachineDefinition) {
  const machine = {
    value: stateMachinDefinition.initialState,
    transition(currentState, event) {
      // get the current state machine state
      const currentStateDefinition = stateMachineDefinition[currentState];

      // get the next state's transition data - if it doesn't exist, early exit
      const destinationTransition = currentStateDefinition.transitions[event];
      if (!destinationTransition) {
        return;
      }

      // get the next state from the next state's transition data
      const destinationState = destinationTransition.target;
      const destinationStateDefinition = stateMachineDefinition[destinationState];

      // Execute the "when transitioning" action
      destinationTransition.action();
      // Execute the current state's "exiting" action
      currentStateDefinition.actions.onExit();
      // Execute the next state's "entering" action
      dstinationStateDefinition.actions.onEnter();

      return machine.value;
    }
  };
  return machine;
}
```
