# [Demystifying OAuth 2.0 And OpenID Connect (and SAML)](https://medium.com/hackernoon/demystifying-oauth-2-0-and-openid-connect-and-saml-12aa4cf9fdba)

* Situation is a contact list on Google's contact server for a given user
  * User wants to give another application (like Yelp) ability to access the contacts
  * User can go through OAuth 2.0 flow and delgate authorization to contacts via authorization server
* User is considered "resource owner"
* Contacts server is considered "resource server"
* Yelp app is considered "client"
* Server that authorizes Yelp to access contacts is called "authorization server"
* OAuth 2.0 is different than user attempting to sign in to Yelp using Google account
  * OAuth 2.0 flow is only designed to grant authorization and _not_ authenticate
* User flow is that user is on Yelp and clicks button that says "import contacts from google"
  * Yelp sends request to google with `client_id`, `redirect_uri`, `scope`, `response_type`, and a `state` parameters
    * `redirect_uri` is just the URI the authorization server will call once it process authorization request
    * `scope` defines what type of resource access should be granted to (like `contacts`)
    * `response_type` specifies what the authorization server will provide the client - in this case, and authorization code that the client will exchange for an access token
    * `state` is a parameter that the authorization server sends later that ensures there's no man-in-the-middle
  * Google constructs a consent page that the user must click to grant consent to Yelp
  * If user consents, then authorization server redirects the user and then the client now has an authorization code that it can exchange for an access token and fetch the contact data

## Federated Authentication

* The ability to login to an app using Facebook / Google / etc login
* OAuth 2.0 was designed for delegated authorization while OpenID Connect is OAuth 2.0 + a couple additional steps to allow for federated authentication
* In addition to access token, an ID token is returned by authorization server
* `openid` is passed as the `scope` parameter (and not `contacts`)
* `ID` token is typically returned as a JWT