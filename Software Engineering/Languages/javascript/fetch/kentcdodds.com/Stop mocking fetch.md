# [Stop mocking fetch](https://kentcdodds.com/blog/stop-mocking-fetch)
* End up re-implementing backend when mocking `fetch`, often times across multiple tests

```javascript
client.mockResolvedValueOnce(() => ({ success: true }))

userEvent.click(screen.getByRole('button', { name: /confirm/i }))

expect(client).toHaveBeenCalledWith('checkout', { data: shoppingCart })
expect(client).toHaveBeenCalledTimes(1)
expect(await screen.findByText(/success/i)).toBeInTheDocument()
```

```javascript
window.fetch.mockResolvedValueOnce({
  ok: true,
  json: async () => ({ success: true }),
})

userEvent.click(screen.getByRole('button', { name: /confirm/i }))

expect(window.fetch).toHaveBeenCalledWith(
  '/checkout',
  expect.objectContaining({
    method: 'POST',
    body: JSON.stringify(shoppingCart),
  }),
)
expect(window.fetch).toHaveBeenCalledTimes(1)
expect(await screen.findByText(/success/i)).toBeInTheDocument()
```

* One thing that has worked well for the author is a single mock fetching function that re-implements parts of the backend

```javascripot
// add this to your setupFilesAfterEnv config in jest so it's imported for every test file
import * as users from './users'

async function mockFetch(url, config) {
	switch (url) {
		case '/request1': {
			const user = await users.login(JSON.parse(config.body))
			return {
				ok: true,
				status: 200,
				json: async () => ({ user }),
			}
		}
		case '/request2': {
			// etc
		}
		default: {
			throw new Error(`Unhandled request: ${url}`)
		}
	}
}

beforeAll(() => jest.spyOn(window, 'fetch'))
beforeEach(() => window.fetch.mockImplementation(mockFetch))
```

* Happy path tests don't need to do anything
* May need a custom mock `fetch` for error handling
* A mock service worker (or mock server) intercepts requests and handles them as if it was a real server
* The "database" would be built from `json` files to "seed" the database or "builders" via faker
* Can also use the same server handlers in the browser during development
  * Benefits when the endpoint isn't ready / broken

```javascript
// server-handlers.js
// this is put into here so I can share these same handlers between my tests
// as well as my development in the browser. Pretty sweet!
import { rest } from 'msw' // msw supports graphql too!
import * as users from './users'

const handlers = [
	rest.get('/request1', async (req, res, ctx) => {
		const user = await users.login(JSON.parse(req.body))
		return res(ctx.json({ user }))
	}),
	rest.post('/request2', async (req, res, ctx) => {
		// etc
		return res(ctx.json({ success: true }))
	}),
]

// test/server.js
import { rest } from 'msw'
import { setupServer } from 'msw/node'
import { handlers } from './server-handlers'

const server = setupServer(...handlers)

// test/setup-env.js
// add this to your setupFilesAfterEnv config in jest so it's imported for every test file
import { server } from './server.js'

beforeAll(() => server.listen())
// if you need to add a handler after calling setupServer for some specific test
// this will remove that handler for the rest of them
// (which is important for test isolation):
afterEach(() => server.resetHandlers())
afterAll(() => server.close())
```

* Server handlers are not in the same location as the tests that rely on those server handlers - no colocation of related data
* Want to colocate the things that are important and unique to your test
* For errors and edge-cases, MSW has the ability to add additional server handlers at runtime within a test
  * Can reset the original handlers to preserve test isolation

```javascript
// edge/error case, special server stuff
// note that the afterEach(() => server.resetHandlers()) we have in our
// setup file will ensure that the special handler is removed for other tests
test('shows server error if the request fails', async () => {
	const testErrorMessage = 'THIS IS A TEST FAILURE'
	server.use(
		rest.post('/checkout', async (req, res, ctx) => {
			return res(ctx.status(500), ctx.json({ message: testErrorMessage }))
		}),
	)
	const shoppingCart = buildShoppingCart()
	render(<Checkout shoppingCart={shoppingCart} />)

	userEvent.click(screen.getByRole('button', { name: /confirm/i }))

	expect(await screen.findByRole('alert')).toHaveTextContent(testErrorMessage)
})
```
