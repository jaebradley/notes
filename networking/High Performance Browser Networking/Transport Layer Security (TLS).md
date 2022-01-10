# Transport Layer Security (TLS)

* SSL protocol was original developed at Netscape to enable ecommerce transaction security
  * Developed on top of TCP
* A third-party observer should only infer the connection endpoints, type of encryption, as well as frequency and an approximate amount of data sent, but cannot read or modify any of the actual data

## Encryption, Authentication, and Integrity

* To establish a cryptographically secure data channel, the connection peers must agree on which ciphersuites to use and what keys to encrypt the data
* The reason TLS works is that due to public key / asymmetric key cryptography, peers can negotiate a shared secret key without having to establish any prior knowledge of each other, and can do so over an unencrypted channel
* TLS handshake allows peers to authenticate their identity
  * Allows client to verify that server is who they claim to be using an established chain of trust
  * Allows the server to optionally verify the identify of the client
    * A company server can authenticate all employees, each of whom could have their own unique certificate signed by the company
* The TLS protocol signs each message with a message authentication code (a MAC)
  * MAC is a one-way cryptographic hash function (i.e. a checksum), the keys to which are negotiated by both connection peers
  * Whenever a TLS record is sent, a MAC value is generated and appended for that message, and the receiver is then able to compute and verify the sent MAC value to ensure message integrity and authenticity

## TLS Handshake

* First roundtrip is TCP three-way handshake
* Client sends server a number of specification details over plain text (like which version of the TLS protocol it is running, list of supported ciphersuites, etc)
* Server picks TLS protocol version for further communication, picks a ciphersuite, attaches its certificate, and sends a response back to the client
  * Here is where server could ask for the client's certificate
* Client initiates RSA / Diffie-Hellman key exchange, used to establish the symmetric key for the ensuing session
* Server processes the key exchange parameters sent by the client, checks message integrity by verifying the MAC, and returns an encrypted `Finished` message to client
* Client decrypts message with negotiated symmetric key, verifies the MAC, and tunnel is established

## RSA, Diffie-Hellman and Forward Secrecy

* RSA handshake: client generates a symmetric key, encrypts it with the server's public key, and sends it to the server to use as the symmetric key for the established session
  * Server uses its private key to decrypt the symmetric key, and now both client and server can use negotiated symmetric key to encrypt their session
* Weakness with RSA handshake is that the same public-private key pair is used to authenticate the server and to encrypt the symmetric session key sent to the server
  * If an attacker gained access to the server's private key, then they can decrypt the entire session
  * Even if an attacker does not currently have access to the private key, they can still record the encrypted session and decrypt later once they obtain the private key
* Diffie-Hellman allows client and server to negotiate a shared secret without explicitly communicating it in the handshake
  * The server's private key is used to sign and verify the handshake but the established symmetric key never leaves the client or server and cannot be intercepted by a passive attacker
  * Reduces risk of compromise of past communication sessions by generating a new symmetric key as part of every key exchange
  * Because keys are renegotiated every session, knowing the private key or the current session key does not help the attacker decrypt any previous sessions
* Symmetric key cryptography is used, in large part, for performance reasons as public key cryptography is much more computationally expensive

## Application Layer Protocol Negotiation

* To enable easy deployment of custom protocols, reuse ports 80 / 443 and use an additional mechanism to negotiate the application protocol
* Port 80 is reserved for HTTP and port 443 is reserved for secure HTTPS sessions running over TLS
* The Application Layer Protocol Negoation is a TLS extension that extends the TLS handshake and allows peers to negotiate protcols without additional roundtrips
  * Client appends a `ProtocolNameList` field with list of supported application protocols as part of `ClientHello` message
  * Server returns a `ProtocolName` field selecting one of the protocols that is part of the `ProtocolNameList` as part of the `ServerHello` response

## TLS Session Resumption

### Session Identifiers

* Server creates a 32-byte session identifier as part of the `ServerHello` message
* Both client and server can store the previously negotiated session parameters and reuse them for subsequent sessions
* Client can include the session ID in the `ClientHello` message, which the 
