# [New Account Creation with a Passkey](https://www.passkeycentral.org/design-guidelines/optional-patterns/new-account-creation-with-a-passkey)
* Request username without showing a password
* Include the fallback authentication methods based on security policy/business needs
* Many services for consumers set the `userVerification` requirement to `preferred` (like Google sign-in)

## Benefits
* Decrease account creation time by removing the need to create a password and increasing passkey creation success
* Increase the use of phishing-resistant authentication
* Reduce account recovery concerns as passkeys might be synced across cloud providers to enable passwordless sign-ins across user's devices
* Reduce user cognitive load by eliminating auth methods liks SMS one-time-password

## Flow
<img width="1252" height="1779" alt="image" src="https://github.com/user-attachments/assets/908dc1de-41b2-4f94-98f2-a3216f48a840" />

<img width="1158" height="1016" alt="Screenshot 2025-09-21 at 09 37 24@2x" src="https://github.com/user-attachments/assets/a8e8ca71-3d9f-448b-b674-2f4f74013585" />

<img width="1180" height="1396" alt="Screenshot 2025-09-21 at 09 38 14@2x" src="https://github.com/user-attachments/assets/d94b4ec8-02e6-4b08-92a9-96229ad648cd" />

<img width="1174" height="1390" alt="Screenshot 2025-09-21 at 09 38 26@2x" src="https://github.com/user-attachments/assets/f96f12a9-6444-4c4e-9186-8c843a43e254" />

<img width="1752" height="1558" alt="Screenshot 2025-09-21 at 09 38 46@2x" src="https://github.com/user-attachments/assets/b87e6f73-a08f-4a69-9997-b203b6fd1cd4" />

<img width="580" height="2060" alt="Screenshot 2025-09-21 at 09 39 09@2x" src="https://github.com/user-attachments/assets/8af3bb14-0373-4218-8e1b-94d3fa5a79dd" />

* For many participants, signing in with face or touch was a familiar and trusted process
  * Perceived to be fast, easy, and secure based on experiences with mobile apps and credential manageers
* Privacy and security concerns led some participants to decline or create a passkey
  * Some participants viewed passkeys as single-factor authentication, which they perceived to be less secure than MFA
  * Other reluctant participants described being uncomfortable trusting Apple, Google, etc
* Ensure that users are aware of the compatibility requirements for using passkeys and provide guidance on compatible devices and browsers
* In the native mobile app context, signing in with a passkey differs from the biometric sign-in experience as a passkey requires an additional tap
