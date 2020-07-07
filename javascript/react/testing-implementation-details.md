# [Testing Implementation Details](https://kentcdodds.com/blog/testing-implementation-details)

```jsx
// accordion.js
import React from 'react'
import AccordionContents from './accordion-contents'
class Accordion extends React.Component {
  state = {openIndex: 0}
  setOpenIndex = openIndex => this.setState({openIndex})
  render() {
    const {openIndex} = this.state
    return (
      <div>
        {this.props.items.map((item, index) => (
          <>
            <button onClick={() => this.setOpenIndex(index)}>
              {item.title}
            </button>
            {index === openIndex ? (
              <AccordionContents>{item.contents}</AccordionContents>
            ) : null}
          </>
        ))}
      </div>
    )
  }
}
export default Accordion
```

```javascript
test('setOpenIndex sets the open index state properly', () => {
  const wrapper = mount(<Accordion items={[]} />)
  expect(wrapper.state('openIndex')).toBe(0)
  wrapper.instance().setOpenIndex(1)
  expect(wrapper.state('openIndex')).toBe(1)
})
test('Accordion renders AccordionContents with the item contents', () => {
  const hats = {title: 'Favorite Hats', contents: 'Fedoras are classy'}
  const footware = {
    title: 'Favorite Footware',
    contents: 'Flipflops are the best',
  }
  const wrapper = mount(<Accordion items={[hats, footware]} />)
  expect(wrapper.find('AccordionContents').props().children).toBe(hats.contents)
})
```

## False Negatives

* When refactoring to allow for multiple accordion items to be opened at once, `openIndex` is swapped for an array of `openIndexes` and instead of `index === openIndex`, this is swapped for `openIndexes.includes(index)`
  * But now, tests are broken - `wrapper.state('openIndex')` should be `wrapper.state('openIndexes')` and it should equal `[0]` not `0`
  * This is a false negative because there was a test failure even though the application code was not broken

## False Positives

* The click handler gets refactored from an anonymous function and tests pass though application breaks
  * Test breaks because while test verifies state changes when `setOpenIndex` is called, it didn't verify that the button referenced `setOpenIndex`

```jsx
<button onClick={this.setOpenIndex}>{item.title}</button>
```

## Example of Implementation Detail-Free Test Using `react-testing-library`

```jsx
test('can open accordion items to see the contents', () => {
  const hats = {title: 'Favorite Hats', contents: 'Fedoras are classy'}
  const footware = {
    title: 'Favorite Footware',
    contents: 'Flipflops are the best',
  }
  const {getByText, queryByText} = render(
    <Accordion items={[hats, footware]} />,
  )
  expect(getByText(hats.contents)).toBeInTheDocument()
  expect(queryByText(footware.contents)).toBeNull()
  fireEvent.click(getByText(footware.title))
  expect(getByText(footware.contents)).toBeInTheDocument()
  expect(queryByText(hats.contents)).toBeNull()
})
```

* Implementation detail-free since tests pass regardless of what the index tracking state is called and also handles previous case of if the button click handler is incorrectly referenced

## What Are Implementation Details

* Users for React components are typically end-users and developers
  * End users will see what is rendered
  * Developers will generally interact with a component via props
  * So tests should typically interact with the props that are passed in and the rendered output
* React Testing library passes fake props to the example `Accordion` component and then interacts with the rendered output by querying the output for contents that are displayed to the user (or are _not_ displayed to the user)
  * Also allows clicking buttons / other rendered UI
* Enzyme test needed to access state (`openIndex`)
  * Neither end users or developers really care about it directly when using the `Accordion` component
  * They don't know what it's called, or if it's stored in an array or as a single value and they don't / shouldn't care - yet test knows about these kinds of implementation details
