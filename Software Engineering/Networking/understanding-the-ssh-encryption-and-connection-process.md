# [Understanding The SSH Encryption And Connection Process](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process)

* SSH stands for "secure shell" and is a secure protocol as well as a mechanism for establishing a cryptographically secured connection between two parties
* Symmetrical encryption is a type of encryption where one key can be used to encrypt messages to the opposite party as well as decrypt messages received from the other particpant
  * In other words, anybody holding the key can encrypt _and_ decrypt messages to anyone else holding the key
  * Asymmetrical public/private keys are only used for _authentication_ not encrypting messages passed between client and server
  * This symmetrical encryption is generated through a process called key exchange algorithm, which uses results from both the server and client using shared data to arrive at the same key value
  * This encryption is done prior to authenticating the client
* Asymmetrical encryption involves a private key and a public key
  * As the name suggests, the public key can be shared with any party, but the private key must not
  * The private key allows the public key to encrypt messages that can _only_ be decrypted by the private key
  * This means that not even the public key can decrypt the messages it writes, nor can it decrypt anything the private key may send it
  * However, this means that any entity that can decrypt the sent message demonstrates that they have access to the associated private key
* SSH uses public / private key values to set up the symmetrical encryption in the initial key exchange process
* SSH key-based authentication is when SSH key pairs are used to authenticate a client with a server
  * The client creates a key pair and uploads the public key to the `~/.ssh/authorized_keys` file any remote server it wants to access
  * The server can use the public key in this file to encrypt a challenge message to the client and if the client can decrypt the message it has demonstrated that it owns the associated private key
* Using the same hashing function and message should produce the same hash; modifying any portion of the data should produce an entirely different hash
  * A user should _not_ be able to produce the original message from a given hash, but they _should_ be able to tell if a given message produced a given hash

## Negotiating Encryption For The Session

* Client and server negotiate a session key using a version of something called Diffie-Hellman
  * Algorithm combines private data from one party with public data from the other party to theoretically arrive at the same secret (symmetric) session key
  * This session key will be used to encrypt the session - the private and public keys used are completely separate from the SSH keys used to authenticate a client to the server
* Diffie-Hellman procedure:
  * Large prime number is the seed value
  * Some type of agreed-upon encryption generator (like AES)
  * Each party comes up with a secret large prime number - used as the "private key" for this interaction and is different than the private SSH key used for authentication
  * The generated private key, the seed value, and the encryption generator are used to generate a public key that is shared with the other party
  * Both parties exchange their public keys
  * Each party uses their private key, the other party's public key, and the original seed value to compute a shared secret key that should be the same between the two parties
  * The shared secret is used to encrypt all communication that follows

## Authentication

* Client sends an ID for the SSH key/pair it would like to authenticate with to the server
* Server checks the `authorized_keys` file for the key ID that the client has specified
* If a public key with the matching ID is found, the sever generates a random number and uses the public key to encrypt the number
* Then the sever sends the client this encrypted value
* The client then decrypts this value, combines it with the shared session key used to encrypt the session, and calculates the MD5 hash of this value
* The client sends this MD5 hash value to the server
* The server calculates an MD5 value on its own where it combines the shared session key and the original number it sent to the client
* If the two values match, it proves the client was in possession of the private key
