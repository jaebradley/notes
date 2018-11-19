# [React's Render Props in Practice](https://medium.com/myheritage-engineering/reacts-render-props-in-practice-b3775fcbf3c3)

* Existing `Pager` component that contains `Prev` / `Next` arrow buttons which shifts the current page cursor and dispatches an on change event
* New `Pager` component that contains a set of page numbers to select and maintains old arrow buttons' functionality
* Could add a `variant` / `prop` to the component used to identify which "type" of `Pager` component it is
* If we want to add more functionality / freedom (i.e. being able to choose the separator, have the `prev` and `next` buttons be actual buttons) we create placeholders for the `prev` / `next` buttons which encapsulate how the `Pager` component is implemented
* The `Prev` and `Next` compound components both get props of their own and render their children appropriately
* The way these compound components interact with the "parent" components is via the React Context API
  * The parent component's context wraps it's children's rendering and gives them access to the `cursor`, `pagesCount`, and `changePage` props
  * The `Prev` and `Next` components are called as the `Pager`'s `children` props

```jsx
// Implementation of Pager component

import React from "react";
import PropTypes from "prop-types";

/**
 * For enabling nested elements to access the component's state.
 */
const PagerCompoundContext = React.createContext();

class PagerCompound extends React.Component {
  static Prev = ({ children }) => (
    <PagerCompoundContext.Consumer>
      {context => (
        <div onClick={() => context.changePage(context.cursor - 1)}>
          {context.cursor > 0 ? children : "N/A"}
        </div>
      )}
    </PagerCompoundContext.Consumer>
  );

  static Next = ({ children }) => (
    <PagerCompoundContext.Consumer>
      {context => (
        <div onClick={() => context.changePage(context.cursor + 1)}>
          {context.cursor < context.pagesCount ? children : "N/A"}
        </div>
      )}
    </PagerCompoundContext.Consumer>
  );

  state = {
    cursor: this.props.cursor || 0,
    pagesCount: this.props.pagesCount || 0
  };

  changePage = newCursor => {
    this.setState(
      ({ cursor }) => ({
        cursor: newCursor
      }),
      () => this.props.onPageChange(this.state.cursor)
    );
  };

  render() {
    const { cursor, pagesCount } = this.state;
    return (
      <PagerCompoundContext.Provider
        value={{
          cursor,
          pagesCount,
          changePage: this.changePage
        }}
      >
        {this.props.children}
      </PagerCompoundContext.Provider>
    );
  }
}

// Pager component usage

import PagerCompound from "./components/pager-compound";

<PagerCompound
  pagesCount={10}
  cursor={3}
  onPageChange={cursor => console.log("Page changed:", cursor)}
>
  <PagerCompound.Prev>
    <button>Prev</button>
  </PagerCompound.Prev>
  <div> - </div>
  <PagerCompound.Next>
    <div>
      <button>Next</button>
    </div>
  </PagerCompound.Next>
</PagerCompound>
```

* A component with a `render` prop takes a function that returns a React element and calls it instead of implementing it's own render logic
* The basic `Pager` component should expose it's API to be consumed by other composed components that are more focused on the desired appearance and functionality

```jsx
render() {
    const { cursor, pagesCount } = this.state;
    return this.props.children({
      cursor,
      pagesCount,
      goPrev: () => {
        this.changePage(this.state.cursor - 1);
      },
      goNext: () => {
        this.changePage(this.state.cursor + 1);
      },
      changePage: this.changePage
    });
  }
```

* Then these `render` props are used in the `Prev` / `Next`pager case like

```jsx
function PrevNextPager(props) {
  return (
    <PagerBasic {...props}>
      {({ cursor, pagesCount, goPrev, goNext }) => {
        const prevBtnText = cursor - 1 < 0 ? "N/A" : "< Previous";
        const nextBtnText = cursor + 1 < pagesCount ? "Next >" : "N/A";
        return (
          <div>
            <span onClick={goPrev}>{prevBtnText}</span>
            {<span> | </span>}
            <span onClick={goNext}>{nextBtnText}</span>
          </div>
        );
      }}
    </PagerBasic>
  );
}
```

* Note how inside `PagerBasic` there's a function that returns some `JSX`
* The inner function uses the render props passed via the `render` function decoupling the `onPrevious` and `onNext` behavior with the actual presentational implementation (i.e the actual `div`s and `span`s used)
