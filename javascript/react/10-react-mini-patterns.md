# [10 React Mini-Patterns](https://hackernoon.com/10-react-mini-patterns-c1da92f068c5)

## Fixing HTML Inputs

* Build separate components for each input "type"
  * So build a `<Checkbox />` or `<RadioButton />` components
* `onChange` should return input value of some sort vs. JavaScript `Event`
* `onChange` should return value in same type as value passed in
  * So if `props.value` is a number then `onChange` should return numbers

## Unique `id`s for `<label>` and related `<input>` elements

* Incrementing id that gets reset on render

```javascript
class Input extends React.Component {
  constructor() {
    // Auto-increments id
    this.id = getNextId();
  }

  render() {
    return (
      <label htmlFor={this.id}>
        Some Label
        <input
          id={this.id}
          value="Some Value"
        >
      </label>
    );
  }
}
```

## `CSS` and `props`

* A themes-based approach involves passing a `theme` `prop` like `<Button theme="secondary">Some Button Text</Button />`
  * Should try and require only one theme per component
* Use flags
  * For rounded corners pass a `boolean` `rounded` `prop` value
* Explicitly define the `CSS` property as as `prop` on the component
  * `<Icon width="25" height="25" />`

## Swap switches for object mappings for conditional component rendering

* Instead of using `switch` statements to decide what page to render from a set of pages, use an object

```javascript
const PAGES = {
  home: HomePageComponent,
  about: AboutPageComponent,
}
```

## Getting references to a component

* Use `ref`s vs. `document.getElementById('some-hard-coded-id')`

```javascript
// Here's a focus example

class Input extends React.Component {
  focus() {
    this.inputElement.focus();
  }

  render() {
    return (
      <input ref={(el) => this.inputElement = el;} />
    );
  }
}
```

## Small components could be methods and not components

```javascript
const SearchSuggestions = (props) => {
  // renderSearchSuggestion() behaves as a pseduo SearchSuggestion component
  // keep it self contained and it should be easy to extract later if needed
  const renderSearchSuggestion = listItem => (
    <li key={listItem.id}>{listItem.name} {listItem.id}</li>
  );
  
  return (
    <ul>
      {props.listItems.map(renderSearchSuggestion)}
    </ul>
  );
}
```

## Build store in a way that minimizes component manipulation of data

* Work out general data requirements and structure of components
* Design store to support requirements
* Manipulate incoming data to support these requirements
* Component shouldn't worry about data being passed in as a string vs. a number or boolean is actually string `"true"`

```javascript
fetch('some data url')
  .then(response => response.json)
  .then(normalizeData)
  .then(data => dispatch({ type: FETCHED_DATA, data })
```

