# [Passkeys - Under the Hood](https://research.kudelskisecurity.com/2024/03/14/passkeys-under-the-hood/)

* Service / website authenticates client by asking the client to sign a randomly generated challenge with the client's private key, matching the client's public key, known by the service
* Service only stores the public key, and if the service is breached, it cannot leak information about the client's private key
* The service's address is stored by the browser, thwarting phishing attacks

## `WebAuthn.io` example

* `webauthn.io` requires generation of a public key with specific algorithms
* Browser generates a public key as well as a "random id", to support registering several passkeys for the same login
* The relevant private key is stored on the client side, and cannot be leaked by the service / website
* When the user authenticates for the same website, the website is asking the user to sign a "challenge"
  * This "challenge" can be "signed" with a passkey or a hardware security key
* The `clientDataJSON` field in the successful authentication response case is a base-64 encoded that contains information like the challenge value, and an `origin` representing the base URL of the signing service
  * The `origin` field is read by the browser and is used by the browser to detect any phishing