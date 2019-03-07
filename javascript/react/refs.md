# [Refs](https://hackernoon.com/refs-in-react-all-you-need-to-know-fb9c9e2aeb81)

## Update state based on event handler without using refs

```javascript
import React from 'react';

class Foo extends React.Component {
  constructor() {
    super();

    this.onChange = this.handleOnChange.bind(this);

    this.state = { bar: 'baz' }
  }

  handleOnChange(e) {
    this.setState({ bar: e.target.value });
  }

  render() {
    return (
      <div>
        <input type={'text'} onChange={this.onChange} />
      </div>
    );
  }
}
```

## Update state based on event handler using string ref (not recommended)

* String refs are considered legacy and will probably be deprecated int he future

```javascript
import React from 'react';

class Foo extends React.Component {
  constructor() {
    super();

    this.onChange = this.handleOnChange.bind(this);

    this.state = { bar: 'baz' }
  }

  handleOnChange(e) {
    this.setState({ bar: this.refs.exampleInput.value });
  }

  render() {
    return (
      <div>
        <input type={'text'} ref={'exampleInput'} onChange={this.onChange} />
      </div>
    );
  }
}
```

## Update state based on event handler using ref with callback (recommended)

```javascript
import React from 'react';

class Foo extends React.Component {
  constructor() {
    super();

    this.onChange = this.handleOnChange.bind(this);
    this.handleExampleInputRef = this.setExampleInputRef.bind(this);

    this.state = { bar: 'baz' }
  }

  setExampleInputRef(node) {
    this.a = node;
  }

  handleOnChange(e) {
    this.setState({ bar: this.refs.exampleInput.value });
  }

  render() {
    return (
      <div>
        <input type={'text'} ref={this.handleExampleInputRef} onChange={this.onChange} />
      </div>
    );
  }
}
```

## Notes

* To retrieve a ref pointing to a custom component, you need to do `ReactDOM.findDomNode(this.ref.{myRefSelector})`
* Refs are set after `render` but before `componentDidMount`
* Refs should be used for
  * managing focus, text selection, media playback
  * triggering imperative animations
  * integrating with 3rd party DOM libraries
