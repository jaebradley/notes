# [Sender](https://explained-from-first-principles.com/email/#group-construct)

* `From` field contains the address of the person who is responsible for the content of the message
* `Reply-To` field indicates the address(es) to which replies should be sent
  * If absent, replies are sent to the `From` address
* If the author of the message is not the sender, the sender should be provided in the `Sender` field
* Strongly advise against using an empty group in the `From` field because defeats efforts towrads domain authentication
