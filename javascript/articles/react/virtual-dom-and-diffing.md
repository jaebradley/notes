# [Virtual DOM and Diffing](https://medium.com/@gethylgeorge/how-virtual-dom-and-diffing-works-in-react-6fc805f9f84e)

* Each time something in the DOM changes, the changed element and it's children have to go through `Reflow/Layout` stage and then the changes have to be `Repainted`, which is slow.
* Thus, the more items to reflow and repaint, the slower the app becomes.

## Calculator App Example

* Two inputs and an `Add` button.
* When `Add` is clicked
  * All DOM event listeners are wrapped with custom React event listeners
  * Thus, on click, the event is dispatched to the React `onClick` listener which executes the anonymous function
  * In the anonymous function, `this.setState` is called
  * The `setState` function will mark the `Calculator` component as "dirty"
    * The reason the `Calculator` and not the `Button` is marked "dirty" is because `this` is in the context of the `Calculator`
  * React runs batch update
    * It checks if there are any dirty components and starts updating
    * It checks if there is any pending state that needs to be updated or a force update that was issued
  * Event Lifecycle
    * `componentWillReceiveProps`
    * `shouldComponentUpdate`
    * `componentWillUpdate`
    * `render`
    * `componentDidUpdate`

  ### What happens when we render?

  * React checks if the previous and next rendered elements are of the same type and key
    * If so, then the component's `render` is called

* Reconciliation after clicking `Add`
  * Starts from component and then works way through tree
  * Since `h2` containing `Output: 150` changed, React will update DOM

