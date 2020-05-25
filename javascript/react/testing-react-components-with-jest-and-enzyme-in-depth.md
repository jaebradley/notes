# [Testing React Components With Jest And Enzyme - In Depth](https://blog.bitsrc.io/how-to-test-react-components-with-jest-and-enzyme-in-depth-145fcd06b90)

* One component should only have one snapshot
  * When you need to test the behavior of a component in two states - like before opening a pop-up and after opening a pop-up
  * First test can store default state without popup in a snapshot and second test simulates the event and checks presence of component or CSS class - no need for multiple snapshots
* Test that default prop values are rendered correctly and check custom values of props (passed in custom value and expected to receive it after render of component)
  * Some argue that prop types testing tests a third-party package and shouldn't actually be tested, however, not testing package functionality, but rather that components _props_ are correct
* Test events by mocking an event callback function via Jest, simulating the event, and then expecting event callback is called (with any passed parameters)
  * In addition to expecting any callbacks, assert that any change in behavior (like change in state or components being rendered / unrendered) occurs

## Example for testing a DateInput component

* It has a `showMonthYearsDropdown` `prop` which when set to `true` shows the dropdown date picker

```jsx
it('check month and years dropdowns displayed', () => {  
    const props = {
            showMonthYearsDropdowns: true
        },
        DateInputComponent = mount(<DateInput {...props} />).find('.datepicker');
    expect(DateInputComponent.hasClass('react-datepicker-hide-month')).toEqual(true);
});
```

* Test various `value``prop` values

```jsx
it('render date input correctly with null value', () => {  
    const props = {
            value: null
        },
        DateInputComponent = mount(<DateInput {...props} />);
    expect((DateInputComponent).prop('value')).toEqual(null);
});

it('check the type of value', () => {  
    const props = {
            value: '10.03.2018'
        },
        DateInputComponent = mount(<DateInput {...props} />);
    expect(DateInputComponent.prop('value')).toBeString();
});
```

* Test `onChange` event by mocking the `onChange` callback that is passed via `prop`s and then simulate `change` event with a new value and check that mocked function was called with the value

```jsx
it('check the onChange callback', () => {  
    const onChange = jest.fn(),
        props = {
            value: '20.01.2018',
            onChange
        },
        DateInputComponent = mount(<DateInput {...props} />).find('input');
    DateInputComponent.simulate('change', { target: {value: moment('2018-01-22')} });
    expect(onChange).toHaveBeenCalledWith('22.01.2018');
});
```

* Test that popup opens after clicking on date input
  * Find the date input and simulate a `click` event on it
  * Then assert that popup exists (is done by asserting that the popup with the appropriate class is present)

```jsx
it('check DatePicker popup open', () => {  
    const DateComponent = mount(<DateInput />),
        dateInput = DateComponent.find("input[type='text']");
    dateInput.simulate('click');
    expect(DateComponent.find('.react-datepicker')).toHaveLength(1);
});
```