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
* Client can include the session ID in the `ClientHello` message, which the server can use to remember the negotiated cipher suite and keys from previous handshake
  * Removes the need for a full roundtrip as well as the overhead of public key cryptography, which is used to negotiate the shared secret key
* If the browser requires multiple connections to the same host, it will often intentionally wait for the first TLS negotiation to complete before opening additional connections to the same server, such that they can be "resumed" and reuse the same session parameters
* Session identifiers is limited by the requirement for the server to create and maintain a session cache for every client

### Session Tickets

* If the client indicates that it supports session tickets, the server can include a `New Session Ticket` record, which includes all of the negotiated session data encrypted with a secret key known only by the server
* The sesion ticket is stored by the client and can be included as part of the `ClientHello` message
* All session data is stored on the client, but the ticket is safe because it is encrypted with a key known only by the server
* Simplifies deployment since the client provides the session ticket on every new connection to the server until the ticket expires
* Deploying session tickets across a set of load-balanced servers also requires that all servers must be initialized with the same session key and an additional mechanism to periodically and securely rotate the shared key across all servers

## Chain of Trust and Certificate Authorities

* Pubic key encryption allows us to use the public key of the sender to verify that the message was signed with the right private key, but the decision to approve the sender is still one that is based on trust
* In the Alice / Bob case, let's say they exchanged public keys when they met in person, so they are certain that their exchanges are not compromised by an imposter
* Alice receives a message from Charlie, whom she has not met
  * Charlie claims to be a friend of Bob's - to prove this fact, Charlie asks Bob to sign Charlie's public key with Bob's private key
  * Charlie signs his encrypted public key to Alice
  * Alice checks Bob's signature of Charlie's key. She knows Bob's public key and is thus able to verify that Bob did indeed sign Charlie's key
  * Alice trusts Bob's decision to verify Charlie, Alice accepts the message
  8 This is a chain of trust: Alice trusts Bob, Bob trusts Charlie, thus Alice trusts Charlie
* Every browser and operating system provides a mechanism for you to manually importa ny certificate you trust
* Certificate authorities are trusted third parties that are trusted by the owner of the certificate and the party relying upon the certificate
* Every operating system and most browsers have a list of well-known certificate authorities so you trust the vendors of this software to provide and maintain a list of trusted parties

## Certificate Revocation List

* Every certifiate authority maintains and periodically publishes a list of revoked certificate serial numbers
* This list is downloaded, cached, and checked
* The CRL list will only grow longer and each client must retrieve the entire list of serial numbers
* If the CRL was cached by the client before the certificate was revoked then the CRL will deem the revoked vertificate valid until the cache expires
* Fetching the CRL list from the certificate authority may block certificate verification adding latency to the TLS handshake
* If the CRL fetch fails, most browsers allow verification to proceed

## Online Certificate Status Protocol

* OCSP allows the client to query the CA's certificate database directly for serial number while validating the certificate chain
* Provides real-time validation
* The CA must be able to handle the bandwidth of many real-time queries
  * The CA query service must be globally available at all times
  * The client blocks on OCSP requests while validating the certificate chain
  * If browser fails to fetch OCSP query, typically results in verification proceeding
* Adds ~350ms to TLS handshake when successful

## OCSP Stapling

* The server periodicially receives the signed and timestamped OCSP response from the CA
* The server then appends the signed OCSP as part of the TLS handshake, allowing the client to validate both the certificate and the attached OCSP revocation record, signed by the CA
* Client does not leak navigation history to the CA
* Client does not have to block and query the OCSP server

## TLS Record Protocol

* Application data workflow
  * Record protocol receives application data
  * Received data is divided into blocks - 16kb per record maximum
  * Message authentication code is added to each record
  * Data within each record is encrypted using the negotiated cipher
* The encrypted data is passed down to the TCP layer for transport
* Small records incur larger CPU and byte overhead due to record framing, MAC verification
* Large records will have to be delivered and reassembled by the TCP layer before they can be processed by the TLS layer and delivered to your application

## Optimize Connection Reuse

* Minimizing both latency and computational overhead of setting up new TCP & TLS connections is to optimize connection reuse
* Server and proxy configurations should allow keepalive connections
* Many popular servers set aggressive connection timeouts that force a lot of unnecessary renegotiations

## Uncached Origin Fetch

* Using a CDN or proxy server to fetch a resource which may need to be customized per user and is not globally cacheable is known as an uncached origin fetch
* If the client connection is terminated with the nearby server, the handshake latency costs can be dramatically reduced
* CDN / proxy server can maintain a warm connection pool to relay the data to the origin server, allowing a fast response back to the client
* Some CDN providers will use nearby servers on both sides of the connection
  * The client connection is terminated at a nearby CDN node, which then relays the request to the CDN node close to the origin
  * The hop within the CDN network allows the traffic to be routed over the optimized CDN backbone, which can help to further reduce latency between client and origin servers

## Enable TLS False Start

* Session resumption helps returning visitors but does not help in the cases where the visitor is communicating with the server for the first time / previous session has expired
* TLS False Start is a protocol extension that allows the sender to send application data when the handshake is only partially complete
* Once the client has sent the `ClientKeyExchange` record, it already knows the encryption key and can begin transmitting application data

## Optimize TLS Record Size

* All application data delivered via TLS is transported within a record protocol
* Maximum size of each record is 16kb
* If the record spans multiple TCP packets then the TLS layer must wait for all the TCP packets to arrive before it can decrypt the data
  * If any of those TCP packets get lost, reordered or throttled due to congestion control, then the individual fragments of the TLS record will have to be buffered before they can be decoded

## TLS optimizations at Google

* Google's servers use small TLS records that fit into a single TCP segment for the first MB of data, increase record size to 16kb ater to optimize throughput and then reset record size back to a single segment after one second of inactivity
