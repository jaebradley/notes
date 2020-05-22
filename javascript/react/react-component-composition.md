# [React Component Composition](https://www.robinwieruch.de/react-component-composition)

```jsx
const UsernameForm = ({ onSubmit }) => {
  const [username, setUsername] = useState('');
 
  return (
    <form
      onSubmit={event => {
        onSubmit(username);
        event.preventDefault();
      }}
    >
      <label>
        Your name:
        <input
          type="text"
          value={username}
          onChange={event => setUsername(event.target.value)}
        />
      </label>
 
      <button type="submit">Send</button>
    </form>
  );
};
```

* This `UsernameForm` encapsulates all of the fomr in a single React component making it difficult to tear things apart to serve another purmpose other than capturing the name of a user
* Can build a generic `Form`, `InputField`, and `Button` components, that use the `children` `prop` to compose these components

```jsx
const UsernameForm = ({ onSubmit }) => {
  const [username, setUsername] = useState('');
 
  return (
    <Form
      onSubmit={event => {
        onSubmit(username);
        event.preventDefault();
      }}
    >
      <InputField value={username} onChange={setUsername}>
        Your name:
      </InputField>
 
      <Button type="submit">Send</Button>
    </Form>
  );
};
```

* Another benefit of this approach is that if we wanted the Button component to have a colored background, can just add a `color` `prop` for `Button`
  * If the `Button` `color` `prop` didn't exist, and there was just a `Form` component that wrapped `<form>` (like the first `UsernameForm`) then you'd have to pass a `buttonColor` `prop` to the `Form` component
  * However, the `Form` component shouldn't care about any props for the button element (really) - and this pattern will only get worse as things like the button's label need to become dynamic (i.e. a `buttonLabel` `prop`)
  * Soon the `Form` component needs to take care of everything rendered within itself

## Slot Pattern

* Example using a `SplitPane` component that uses `prop`s to take two children and compose them into another component
  * The `SplitPane` component doesn't know what will be rendered in those "slots" - it just receives whatever it is via props and renders it in the slot