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

## [Subaddressing](https://explained-from-first-principles.com/email/#normalization)

* Split local part into two parts using special character (like `+`) into format like `user+tag@gmail.com`
* Emails to `user+tag@gmail.com` will be delivered to `user@gmail.com`
* Supported by Gmail and Microsoft Exchange

## [Alias address](https://explained-from-first-principles.com/email/#normalization)

* Alias address doesn't have a mailbox associated with it, simply forwards all incoming messages to one / several addresses
* Forwarding done by incoming mail server, and the expanded addresses may belong to the same or to different hosts
* Alias addresses can forward messages to other alias addresses, causing mail loops

## [Mailing List](https://explained-from-first-principles.com/email/#mailing-list)

* Address which forwards incoming messages to all subscribers of the list
* List administrator decides who is allowed to send messages to the list and whether each message needs to be approved by a moderator before it is forwarded
* Mailing list software changes envelope of the message, so that automatic responses are sent to the administrator of the list rather than the original sender
