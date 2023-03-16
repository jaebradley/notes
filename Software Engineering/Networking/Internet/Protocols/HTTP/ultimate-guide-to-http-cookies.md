# [Ultimate Guide to HTTP Cookies](https://blog.webf.zone/ultimate-guide-to-http-cookies-2aa3e083dbae)

* Cookies are sent by web server
* Cookies contain text, not binary
* Cookies must be less than 4kb in size
* Cookies are stored by browser
* Cookies are only available to the website that they are set for
  * Websites cannot read cookies of other websites
* Cookies are necessary because web server, on it's own does not keep track of what user a request is coming from
  * Cookie is set using `Set-Cookie` header
  * Browser stores cookie
  * When user goes to facebook.com, browser automatically sends cookie as part of request
  * Facebook server reads cookie and determines if cookie is valid - generally servers keep mapping of cookies
* Cookies by default has the lifetime of a browser window - these are called session cookies
* Permanent cookies can be created by specifying an `Expiry` header when setting the cookie

## Scoping Cookies

* By default, browsers will set the domain of the cookie to the host of the current document
* `Path` can be specified, but by default it will be the path of the URL that sent the `Set-Cookie` header
  * `http://example.com/test` implies that cookie would be set for `http://example.com/test`, `http://example.com/test/xyz`, etc.
* When `Domain` is set, browser sends cookie for any sub-domain of specified domain
  * `Domain=google.com` means that `mail.google.com` or `drive.google.com` would receive cookies
* By default, cookies is not sent to any subdomain of the domain setting the cookie

## Attacks

* Man-in-the-middle attack: use `HTTPS`-only cookies (cookies are exchanged, if and only if, you use `HTTPS` connection)
  * Set using `Secure` directive when setting up cookie
* Cross-site scripting - some loaded JS can read website's cookie since all JS code on page is considered running in that domain even if it's JS code from a CDN
  * Use `HttpOnly` directive so that cookies are not accessible via `document.cookie` API
  * This will disable your scripts ability to read cookies
* Cross-site request forgery
  * Two websites - legitimate bank website and evil website
  * User logs into bank website and gets cookie
  * There is some code like `<img src="bank.com/withdraw?fromAccount=X&toAccount=Y` on evil website
  * When user navigates to evil website, browser will send cookie for `bank.com` even if request comes from another website (hence the _cross-site_)
  * Server should use `referer` header to reject requests from sources it does not expect requests from (like `evilwebsite.com`)

## Keeping track of cookies in servers

* Cookies might be managed by a load balancers that forwards requests to servers
* Load balancer needs to ensure that all servers know about cookies that are issued and revoked)
* Can use database, but all requests would be slowed down by DB query
* Can use faster in-memory database like `Redis`

## Single Page Applications

* Cookies were primarily to preserve identity of user between multiple page requests
  * With SPAs cookies are not really required to identify user
* Authentication in SPA can work like
  * API call is used to retrieve bearer auth token (assuming user inputs credentials correctly)
  * Auth token is saved in `SessionStorage` or `LocalStorage` - token has some lifetime (`JWT` has an expiry, for example)
    * Use `SessionStorage` is token needs to be maintained for single window - use `LocalStorage` is needs to persist across multiple tabs/windows
* Cookies do two things that are nice
  * They are passed automatically on every request to server
  * Cookie lifetimes are more precise and natural
    * Consider storing things in `LocalStorage` - data can only be delted manually, which can open security holes
    * Handling images becomes difficult - if image is protected resource, then browser will not automatically send token when requesting path (can do this with `fetch` API)
    * Cookies will automatically be deleted (if session if window closes and by browser once persistent cookies expire)
    * Use cookie as storage mechanism and token as authentication mechanism and put it in cookie
