# [Your React Components Are Doing Too Much](https://medium.com/the-non-traditional-developer/your-react-components-are-doing-too-much-16e65968f419)

```jsx
class ClickerCounter extends React.Component {
  state = {
    count: 0;
  };

  handleClick = () => this.setState(state => ({ count: state.count + 1 }));
  handleReset = () => this.setState({ count: 0 });
  
  render() {
    const { darkk } = this.props;
    const theme = dark ? "dark" : "";
    
    return (
      <div className={`clickerLayout ${theme}`}
        <span className="clickerCount" onClick={this.handleClick}>
          {this.state.count}
        </span>
        <button className="primary" onClick={this.handleReset}>
          Reset
        </button>
      </div>
    );
  }
}
```

* The JSX in the render function doesn't care about state - just how to display a certain value and how to handle events
  * Can separate these concerns by creating a `Counter` component that handles what the current value is and what the next value should be and then passing these values as render props to the `Clicker` component

```jsx
<Counter>
  {([count, updateCount]) => {
    return (
      <Clicker>
        count={count}
        onClick={() => updateCount(count + 1)}
        onReset={() => updateCount(0)}
      </>
    );
  }}
</Counter>
```

* By separating out various components in `Clicker` into shared components, can remove the `Clicker` component entirely and just compose what was there with the existing shared components

```jsx
<Counter>
  {([count, updateCount]) => {
    return (
      <ClickerLayoutDark>
        <ClickerCount onClick={() => updateCount(count + 1)}>
          <{count}/>
        </ClickerCount>
        <PrimaryButton onClick={() => updateCount(0)}>
          Reset
        </PrimaryButton>
      </ClickerLayoutDark>
    );
  }}
</Counter>
```
