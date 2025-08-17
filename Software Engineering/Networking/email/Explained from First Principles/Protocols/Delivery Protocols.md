# [Delivery Protocols](https://explained-from-first-principles.com/email/#delivery-protocols)

## [Submission versus relay](https://explained-from-first-principles.com/email/#submission-versus-relay)
* SMTP is used by the mail client to submit a message to the outgoing mail server of its user
* SMTP is used by the outgoing mail server to relay the message to the recipients' incoming mail servers
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

## [Diverging envelope example](https://explained-from-first-principles.com/email/#diverging-envelope-example)

<img width="814" alt="image" src="https://user-images.githubusercontent.com/8136030/226629120-a0c5faf0-cc35-49f7-81e3-1b5d44c7ee54.png">

<img width="817" alt="image" src="https://user-images.githubusercontent.com/8136030/226629183-4399918f-ef90-470f-a661-db4716094fe4.png">

<img width="835" alt="image" src="https://user-images.githubusercontent.com/8136030/226629270-b9bf3e6c-01db-4635-8882-9ee188a6fd3d.png">

<img width="836" alt="image" src="https://user-images.githubusercontent.com/8136030/226629356-ff4f330c-87b2-4f03-be6b-0064158bfee0.png">

## [Who removes the Bcc header field?](https://explained-from-first-principles.com/email/#bcc-removal)
* RFC 5322 allows four different behaviors for Bcc recipients
1. Complete removal of the Bcc field
  * Delivers message with a single envelope for all recipients to the outgoing mail server
  * Most common behavior in practice
  * Advantage is that the mail client submits the message once to the outgoing mail server
  * Disadvantage is that Bcc recipient don't know learn why they have received the message
  * Hidden recipients shouldn't send a response to non-hidden recipients because this discloses the fact that they also received the message
  * Mail clients should warn users when they click reply all for emails that weren't addressed to them
  * The only way to be sure that a Bcc recipient won't reply all by accident is
   * First send the message to the non-Bcc recipients
   * Then forward the message to hidden recipients
   * If the hidden recipients don't need to be hidden from each other, then they can be listed in the To field of the forwarded email
    * Otherwise, keep them in the Bcc field
2. Grouped delivery where the mail client splits the recipients into Bcc and non-Bcc recipient groups
  * Non-Bcc recipients get the message where the Bcc field is removed
  * Bcc recipients get the original message where the Bcc recipients are listed
3. Individual delivery
  * All non-Bcc recipients receive the same message
  * Each Bcc recipient receives separate versions of the message where only they are listed as the Bcc recipient
  * Prevents Bcc recipients from learning about any other Bcc recipients
  * While nice in theory, not ideal in practice because work is now done client-side instead of server side
   * The reason why outgoing mail servers exist is that mail clients only need to submit a message addressed to many recipients once
   * Creating and sending an individual version of each message for each Bcc recipient on the client-side defeats this purpose
