# [The Beer Drinker's Guide To SAML](https://duo.com/blog/the-beer-drinkers-guide-to-saml)

* Beer + ID Analogy
  * Bob walks to Wristband Tent where his ID is checked and wristband is provided
    * Wristband Tent is identity provider - its sole purpose is to verify Bob's identity and to make sure he meets necessary criteria to get a wristband
  * The wristband is the SAML assertion
  * Bob then walks over to the Beer Tent - seller sees Bob's wristband and knows that he is eligible to drink beer (Beer Tent = Service Provider or the thing that Bob actually wants access to)
* The identity provider (i.e. the Wristband Tent) is simply an authority that the service provider trusts - the service provider doesn't really care _how_ the identity provider verifies a user
* The service provider checks the SAML assertion (wristband) to make sure it came from the identity provider that Salesforce trusts
  * Service provider also checks the SAML assertion to see who user is and who they should be logged into Salesforce as
* Often times, there are many service providers configured to a single identity provider (like Okta)
  * Each service provider is kind've like a different "tent" and they all trust a single wristband tent / identity provider
  * The identity provider can issue a different wristband depending on which service the user wants to go to (again, in this analogy a different service might be the wine tent vs. the liquor tent vs. the beer tent)

## IdP-initiated vs. SP-Initiated

* IdP-initiated means that the user first navigates to the identitiy provider's dashboard and then goes to the service provider with a SAML assertion
  * This is like going to the Wristband Tent first then going to the Beer Tent
* Service provider initiated means the user first navigates to the service provider, gets redirected to the identity provider with a SAML request, then redirected back to the service provider
  * The SAML request says "this user is trying to log in but they don't have a SAML assertion yet - please help them get a SAML assertion and then send them back here"
  * In the tent analogy, it's like if the Beer Tent wrote a note on somebody that didn't have a wristband saying "This guy wants a beer, give him a wristband and send him back"

## IdP Configuration

* Specifications for a SAML assertion are provided by the service provider and set at the IdP
  * This is like the Beer Tent dictating what they expect to be on the wristband and the Wristband Tent being made aware of these expectations
* `EntityID` - globally unique name for the service provider - common for value formatted as a URL
* `ACS` (Assertion Consumer Service) - the URL location where the SAML assertion is sent
  * `https://beertent.com/saml/consume`
* Attributes - these are the attributes that are expected on the "wristband"
  * `NameID` - the username of the user trying to log in

## Service Provider Configuration

* Provided by IdP and set by the service provider
* `X.509` Certification
  * This certificate is provided by the IdP and is used to verify the public key passed by the IdP in the metadata of the SAML assertion
  * Allows the service provider to verify that the SAML assertion is actually coming from the IdP it trusts
* Issuer URL - unique identifier of the IdP that is a URL so that the SP can validate that the SAML assertions are from the correct IDP
* SAML SSO Endpoint / Service Provider Login URL - an IdP endpoint that initiates authentication when redirected here by the service provider with a SAML request
