# [How Do Rails Sessions Work](https://www.justinweiss.com/articles/how-rails-sessions-work/)

* Sessions are used to store data
* That data is set in the browser by the server using cookies, specifically HTTP-only cookies
  * Can only store ~4kb in a cookie
  * Cookies are sent with every request
* What `ActiveRecord` does
  * When a user does not have a session `ActiveRecord` will create a random session in the `sessions` table
  * It'll store `{ current_user_id: 1 }` (B64-encoded) in the `data` attribute of that session record
  * It'll return the generated session id to the browser and sets a cookie with that value
* Next web request
  * The browser will send server the session in the cookie like `Cookie: _my_app_session=B64-session-id`
  * Server gets session id out of cookie
  * Server identifies session in `sessions` table
  * Gets `current_user_id` out of `data` attribute of session record
* Cookie should _only_ contain session id
* Need to clean up expired sessions

