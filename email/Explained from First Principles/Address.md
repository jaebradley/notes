# [Address](https://explained-from-first-principles.com/email/#address)

* Consist of a username, followed by the `@` symbol and a domain name
* DNS ensures that the domain name is unique
* Mailbox provider ensures that each username is unique within its domain
* Mailbox can be identified by several addresses
* An email sent to a single address can be delivered to multiple mailboxes

## [Normalization](https://explained-from-first-principles.com/email/#normalization)

* The local part of an email address is the string prefix before the `@` symbol
* The local part of an email address is completely up to the receiving mail system
* It is recommendended, but not mandatory that mail servers treat `John.Smith` and `john.smith` as the same user
  * Some mailbox providers have more specific implementations, Gmail removes all dots from the local part of an address

