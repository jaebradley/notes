# [Entities]([https://explained-from-first-principles.com/email/#official-architecture](https://explained-from-first-principles.com/email/#entities)

## [Mail Client](https://explained-from-first-principles.com/email/#mail-client)

* Mail client is a computer program that composes, sends, retrieves, and reads emails
  * Connects to outgoing mail server to submit messages
  * Connects to incoming mail server to fetch new messages from the user's mailbox
  * Mail client connects to the incoming and outgoing mail server with a username / password
* Simplified mail architecture is
  * Mail client of sender uses SMTP for message submission to the outgoing mail server of sender
  * Outgoing mail server uses SMTP for relaying messages to the incoming mail server of the recipient
  * Recipient mail client uses IMAP or POP3 to retrieve messages from the incoming mail server of the recipient

<img width="703" alt="image" src="https://user-images.githubusercontent.com/8136030/224551004-c78022f0-c523-4629-8222-7f1ed65f7680.png">

## [Outgoing mail server](https://explained-from-first-principles.com/email/#outgoing-mail-server)

* Outgoing mail server accepts messages from mail clients and queues them for delivery
* Determines the incoming mail server for each recipient
* Outgoing mail server acts as a client when relaying messages to each recipient's incoming mail server
  * If server cannot deliver a message, it sends a bounce message to the user that submitted the message
* Outgoing mail server should not change contents of the message, but adds information about the submitter in a header
