# [Delivery Protocols](https://explained-from-first-principles.com/email/#delivery-protocols)

## [Submission versus relay](https://explained-from-first-principles.com/email/#submission-versus-relay)

* SMTP is used by the mail client to submit a message to the outgoing mail server of its user
* SMTP is used b the outgoing mail server to relay the message to the recipients' incoming mail servers
* Historically, mail servers relayed messages from anybody to anybody else
  * No distinction between outgoing mail servers and incoming mail servers
  * Every mail server was a mail transfer agent
  * This was abused by spammers, who routed their mail through the relay servers of reputable organizations
  * Relay servers are not supposed to modify messages, but mail servers started rewriting messages to adhere to the standard and organization-specific policies
* RFC-2476 was introduced to separate submission and relay
  * Mail clients expected to submit outgoing messages on port 587 instead of port 25
  * Allowed submission servers to require authentication before accepting a message
  * Submission servers were permitted to modify messages
  * SMTP now split into two protocols, one for submission and one for relay

## [Message versus envelope](https://explained-from-first-principles.com/email/#message-versus-envelope)

* Outgoing mail servers may add missing header fields / modify the message
* Incoming mail servers should only add trace information to the top of the message
* Information relevant for the handling of the message (addresses to deliver the message to, etc) belongs to the "envelope"
  * Envelope belongs to the SMTP
  * Envelope can change during the delivery of a message - the message should stay the same
  * Emails are delivered based on the addresses in the envelope and not the addresses in the header section of the message
