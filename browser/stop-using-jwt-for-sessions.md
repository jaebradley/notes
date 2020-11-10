# [Stop Using JWT For Sessions](http://cryto.net/~joepie91/blog/2016/06/13/stop-using-jwt-for-sessions/)

* JWT does not prevent CSRF as you can store a JWT in a cookie or in something like LocalStorage
  * If storing in a cookie, still vulnerable to CSRF
  * If storing in Local Storage, now requires JavaScript to work, and opened up to XSS (cross-site scripting) attacks
    * Any attacker can supply JavaScript (as long as it passes the Content Security Policy) to access and exfiltrate the JWT
  * JWTs _need_ to stored in cookies
* JWTs take up more space, quickly exceeding the size limit of a cookie or URL
* Cannot invalidate individual JWT tokens
  * Whereas sessions can be invalidated if a compromise is detected or if a user changes their password
  * Also means that somebody has a token with admin permissions even though their admin role has been revoked

## Valid Uses For JWTs

* A file-hosting service where the user authenticates to download their files, but the files are served by separate stateless "download servers"
  * Application server issues single-use "download tokens" that the client uses to download files from a download server
* JWTs should be short-lived (i.e. a few minutes)
* JWTs should only expected to be used once
* The application server would still use sessions - it's just the download server that uses tokens to authorize individual downloads because it doesn't need to persist state
