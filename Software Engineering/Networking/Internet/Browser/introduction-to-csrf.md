# [Introduction To CSRF](https://medium.com/@charithra/introduction-to-csrf-a329badfca49)

* Trick a user to click a link that send a request to the server that includes any cookies that the victim has associated with the website
* So a URL like `http://example.com/transfer?amount=X&toAccount=UserY` will include the cookie for the authenticated user (if they've logged in)
  * This URL can be part of an `img` tag, which will automatically send this `GET` request
* Can also be done with a `post` request with a form that posts to the vulnerable site (so to `example.com` and not to `bad-website.com` - hence the "cross-site" part of the name, since it's posting _from_ `bad-website.com` to `example.com`)

## Anti-CSRF Tokens

* Client requests an HTML page that contains a form
* Server includes two tokens in the response
  * One token is sent as a cookie
  * Other token is in a hidden form field
* When the client submits the form, it must send back both tokens to the server
* If the request does not contain both matching tokens, the server disallows the request
* The tokens should be invalidated after some period of time and after the user logs out
* A flag can be used on a cookie, making it into a "same-site" cookie
  * This "same-site" cookie will only be sent if the request is made from the same origin that set the cookie
