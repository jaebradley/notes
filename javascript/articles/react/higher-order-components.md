# [Higher Order Component](https://medium.com/@franleplant/react-higher-order-components-in-depth-cf9032ee6c3e)

## [Props Proxy](https://github.com/vasanthk/react-bits/blob/master/ux-variations/04.HOC-props-proxy.md)

* Wrapper that modifies props passed to returned Component
* Usually implemented as a function

```javascript
function propsProxyHOC(WrappedComponent) {
  return class Test extends Component {
    render() {
      const newProps = {
        title: 'New Header',
        footer: false,
        showFeatureX: false,
        showFeatureY: true,
      };

      return <WrappedComponent {...this.props} {...newProps} />
    };
  };
}
```

## [Analytics Use Case](https://github.com/vasanthk/react-bits/blob/master/patterns/34.share-tracking-logic.md)

* Removes tracking logic from presentation component to make testing easier

### Your Analytics HOC

```javascript
import tracker from './tracker.js';

const pageLoadTracking = (WrappedComponent) => class PageLoadTrackingHOC extends Component {
  componentDidMount() {
    tracker.trackPageLoad(this.props.trackingData);
  }

  componentDidUpdate() {
    tracker.trackPageLoad(this.props.trackingData);
  }

  render() {
    return <WrappedComponent {...this.props} />
  }
}
```

### Presentation Component Implementation

```javascript
import LoginComponent from './login';

const LoginWithTracking = pageLoadTracking(LoginComponent);

class ParentComponent extends Component {
  render() {
    const trackingData = { foo: 'bar', user: 'baz' };
    return <LoginWithTracking trackingData={trackingData} />
  }
}
```
