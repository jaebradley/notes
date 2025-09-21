# [New Account Creation with a Passkey](https://www.passkeycentral.org/design-guidelines/optional-patterns/new-account-creation-with-a-passkey)
* Request username without showing a password
* Include the fallback authentication methods based on security policy/business needs
* Many services for consumers set the `userVerification` requirement to `preferred` (like Google sign-in)

## Benefits
* Decrease account creation time by removing the need to create a password and increasing passkey creation success
* Increase the use of phishing-resistant authentication
* Reduce account recovery concerns as passkeys might be synced across cloud providers to enable passwordless sign-ins across user's devices
* Reduce user cognitive load by eliminating auth methods liks SMS one-time-password
