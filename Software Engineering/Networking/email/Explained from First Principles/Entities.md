# [Entities](https://explained-from-first-principles.com/email/#entities)

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

### Necessity of outgoing mail servers

* Technically possible to send emails directly to incoming mail server recipients
* Outgoing mail server typically has a fast and permanent Internet connection unlike mail clients
* Retry strategy
 * An incoming mail server can reject a message
 * An incoming mail server may be unreachable due to maintenance / malfunctioning
 * Standards demand that messages that cannot be delivered immediately have to be queued and retry delivery after a delay of at least 30 minutes, for several days
* Sending a single message to many recipients
 * Mail client only has to submit the message once to an outgoing mail server
 * Outgoing mail server then delivers a copy of the email to each recipient
* Address reputation
 * Incoming mail servers learn the sources of legitimate messages over time
 * Messages from sources with a bad reputation are often dropped on arrival

## [Incoming mail server](https://explained-from-first-principles.com/email/#incoming-mail-server)

* Incoming mail server waits for connections from outgoing mail servers of other users
* When an outgoing mail server connects to transmit a message, the incoming mail server records the message with other information from the session
* Outgoing mail servers evaluate if an accepted message is spam
 * Depending on the spam score, the message is delivered to the recipient's inbox, quarantined to the recipient's spam folder, or discarded without notifying the author
 * Standard explicitly allows incoming mail servers to drop received messages silently

### Address resolution

* Sender identifies the incoming mail server by querying the DNS for mail exchange records of the specified domain name from the email address
 * Mail exchange records assign a priority to each incoming mail server
 * Several servers with the same priority can be used for load balancing
* If no mail exchange records exist, then the sender queries for address records (`A` or `AAAA`) of the domain name

### Null MX record

* Configure a null MX record on all organizational domains that neither send nor receive emails
