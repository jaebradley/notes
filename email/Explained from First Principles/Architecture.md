# [Architecture](https://explained-from-first-principles.com/email/#prefixes)

* Mail user agent - client that composes, sends, receives, and reads emails
* Mail submission agent / server - server to receive outgoing emails from authenticated users
  * Queues outgoing emails for delivery by the mail transfer agent
* Mail transfer agent - server to deliver the queued emails
  * Also receives emails, and forwards received emails to the mail delivery agent
  * The incoming and outgoing mail transfer agents do not have to be the same
* Mail delivery agent - server that receives emails from the local mail transfer agent, and stores the emails in the message store
* Message store - server to store the emails received from the mail delivery agent
  * Delivers email to the mail user agent of the recipient

<img width="788" alt="image" src="https://user-images.githubusercontent.com/8136030/224550335-acc64c16-4115-4765-bec1-3e34463c306f.png">
