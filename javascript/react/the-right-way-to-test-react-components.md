# [The Right Way To Test React Components](https://medium.freecodecamp.org/the-right-way-to-test-react-components-548a4736ab22)

* Understanding a component's contract
  * What it renders
  * The props the components receives
  * The state the components has
  * What the component does when a user interacts with it (on click, on input, etc)
  * What the component does on mount / unmount
* Things to ask
  * What does component render?
  * Does component render different things under different circumstances? i.e. for different props
  * Does the component do anything in state? If so, when does state get invalidated?
  * When user interacts with component, what happens?
* Things not worth testing
  * Tests that duplicate exactly application code
    * Testing in-line styles, for example, might lead to brittle / duplicative tests since changing styles will mean changing the test
  * Tests that duplicate the responsibilities of library code
    * Like prop types - article author doesn't test prop types
* Examples
  * "A `SlideToUnlock` is always rendered - it receives the value of the passed `onUnlocked` prop as its `onSlide` prop, regardless of if it was defined or not"
    * Broken down into
      * A `SlideToUnlock` is always rendered
      * When the passed `onUnlocked` prop is defined, the rendered `SlideToUnlock` receives the prop's value as its `OnSlide` prop
      * When the passed `onUnlocked` props is undefined, the rendered `SlideToUnlock`'s `onSlide` prop should also be set to undefined
      * Two types - some composite component is rendered, and the rendered component receives these props
      * Important to test these as they describe how component interacts with other components
  * If an inline style is changed based on a prop, it needs to be tested

```javascript
// Test Setup

import React from 'react';
import {
  mount,
} from 'enzyme';
import Component from './Component';

describe('Component', () => {
  let props;
  let mountedComponent;

  const component = () => {
    if (!mountedComponent) {
      mountedComponent = mount(
        <Component {...props} />
      );
    }

    return mountedComponent;
  }

  beforeEach(() => {
    props = {
      foo: undefined,
      bar: undefined,
      baz: undefined,
    };
    mountedComponent = undefined;
  });

  // define tests

  describe('when `foo` is defined', () => {
    beforeEach(() => {
      props.foo = 'foo';
    });

    it('renders a subcomponent', () => {
      expect(component().find(SubComponent).length).toBe(1);
    });

    it('passes foo to subcomponent', () => {
      const subcomponent = component().find(SubComponent);
      expect(subcomponent.props('foo')).toEqual(props.foo);
    });
  });
});
```
