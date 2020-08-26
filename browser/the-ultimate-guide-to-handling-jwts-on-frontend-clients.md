# [The Ultimate Guide To Handling JWTs On Frontend Clients](https://hasura.io/blog/best-practices-of-using-jwt-with-graphql/#jwt_vs_session)

* JWTs should have a low expiration time (~15 minutes) because an API that accepts the JWT does an independent verification without depending on the JWT source so the API server does not know if the JWT being used was stolen or not
* JWTs should not be stored on the client via cookies or localstorage as this makes the app vulnerable to CSRF & XSS attacks
* JWTs are not encrypted - they are just base64-encoded and signed
* The benefit of JWTs is that microservices can independently verify a token received from a client was valid without having to access a centralized token database
* The `SameSite` cookie spec will make a Cookie-based approach for storing JWTs safe from CSRF attacks (will only work if authentication and API servers are hosted on same domain)
* Store returned token in a global variable (in-memory)
* When setting up `ApolloLink` middleware, execute logout process if there's no JWT token or if the API responds with a `401`
* To ensure that a JWT can never be used again, can create a set of "invalid" tokens, but this introduces centralized state that every single request will have to check against
* To log out users on multiple tabs, have an event listener for localstorage events, and when the logout key in localstorage is updated, this will nullify the in-memory token, and set the logout item in localstorage

## Refresh Tokens

* With short expiry times for JWTs, without refresh tokens, the user would have to re-authenticate every 15 or so minutes (which is a poor user experience)
  * Same with if they close their window and open it again
* Save the refresh token in an `HttpOnly` cookie which cannot be read by client-side JavaScript
  * When the `/refresh_token` endpoint is called, return a new JWT token and set a new refresh token via the `Set-Cookie` header
* Ensure that refresh tokens have an expiration as well
* When a new session occurs (user opens a new browser tab), try fetching a new JWT if one is not found in-memory by going through the `/refresh_token` workflow
  * If the refresh token has expired, then have the user go through the login flow
* To force a logout on all devices, invalidate all existing refresh tokens

## Server-Side Rendering

* Browser makes request to app URL => SSR server renders page based on user's identity => user gets rendered page and uses app as SPA
* To SSR on authenticated pages, the domain of the authentication API (and the domain associated with the `refresh_token` cookie) must be the same as the domain of the SSR server or else the cookie won't be sent to the SSR server
* SSR server captures the `refresh_token` cookie, gets a new JWT for the user, uses the JWT to make authenticated GraphQL requests to fetch the data necessary to display the page
* To enable additional authenticated requests once the SSR page has loaded, the latest refresh token needs to be set as a cookie on the browser
  * This is because the previous `refresh_token` cookie that the browser knows about was used to generate the SSR page (i.e. used to generate a new JWT) and thus should result in a failed request if used again to generate a new JWT

