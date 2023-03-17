# [Stop Mocking Fetch](https://kentcdodds.com/blog/stop-mocking-fetch)

* Issue with mocking fetch like

```jsx
beforeAll(() => jest.spyOn(window, 'fetch');

// stuff

window.fetch.mockResolvedValueOnce({
  ok: true,
  json: async () => ({ success: true }),
});
```

* Test is lacking assertion that `headers` has the correct `Content-Type` headers and that the correct authentication information being sent
* Issue with mocking things like fetch is that backend gets re-implemented everywhere in the tests
* Have one function that reimplements parts of backend and add it to `setupFilesAfterEnv` so it's imported for every test
* Use `msw` to create a basic mock server that intercepts all requests and handles them as if you were connected to a real server
  * Can make the database from seed files or generate using `faker`
  * Then create handlers that interact with the mocked database

```js
const handlers = [
  rest.get('/login', async (req, res, ctx) => {
    const user = await users.login(JSON.parse(req.body))
    return res(ctx.json({user}))
  }),
  rest.post('/checkout', async (req, res, ctx) => {
    const user = await users.login(JSON.parse(req.body))
    const isAuthorized = user.authorize(req.headers.Authorization)
    if (!isAuthorized) {
      return res(ctx.status(401), ctx.json({message: 'Not authorized'}))
    }
    const shoppingCart = JSON.parse(req.body)
    // do whatever other things you need to do with this shopping cart
    return res(ctx.json({success: true}))
  }),
]
```

* Benefit of creating these handlers is that they take care of what the response properties and headers should be
* If the call to `fetch` is incorrect, then the server handlers won't get called and the test will fail
