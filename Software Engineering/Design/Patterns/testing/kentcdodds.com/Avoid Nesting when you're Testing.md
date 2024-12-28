# [Avoid Nesting when you're Testing](https://kentcdodds.com/blog/avoid-nesting-when-youre-testing)

* Author finds keeping track of where certain variables are defined or reassigned in nested tests problematic
* Author suggests keeping tests flat and inlining (duplicating) shared utility logic
* Author prefers a `setup` function that creates all the shared state and helpers between tests

```
// here we have a bunch of setup functions that compose together for our test cases
// I only recommend doing this when you have a lot of tests that do the same thing.
// I'm including it here only as an example. These tests don't necessitate this
// much abstraction. Read more: https://kcd.im/aha-testing
function setup() {
	const handleSubmit = jest.fn()
	const utils = render(<Login onSubmit={handleSubmit} />)
	const user = { username: 'michelle', password: 'smith' }
	const changeUsernameInput = (value) =>
		userEvent.type(utils.getByLabelText(/username/i), value)
	const changePasswordInput = (value) =>
		userEvent.type(utils.getByLabelText(/password/i), value)
	const clickSubmit = () => userEvent.click(utils.getByText(/submit/i))
	return {
		...utils,
		handleSubmit,
		user,
		changeUsernameInput,
		changePasswordInput,
		clickSubmit,
	}
}
```

* Author actually groups related tests in files to avoid large test files that have a bunch of `describe` blocks
* Will put test helpers in dedicated utility files
