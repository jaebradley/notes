# [The First Few Milliseconds Of An HTTPS Connection](http://www.moserware.com/2009/06/first-few-milliseconds-of-https.html)

## Client Hello

* TLS wraps all traffic in "records" of different types
* The first byte from the browser indicates that this record is a "handshake" record

### Handshake Record

* There is a "random bytes" part of the Client Hello message that is four bytes representing the current UTC and then 28 random bytes
* A list of cipher suites that the browser is willing to support

## Server Hello

* Amazon replies with a handshake record that is 2.5k bytes in size
* The record indicates that Amazon agreed to use the browser's TLS version

### Server Hello Message

* Same "random bytes" format, but with the server's data
* 32 byte session id to reconnect again without going through the entire handshake process
* The cipher suite that Amazon / the server picked

### Certificate Message

* Message is ~2.4k bytes large - it's the certificate that the client uses to validate Amazon.com

### Server Hello Done Message

* 0 byte message that tells client it's done with "Hello" process and indicates that the server won't be asking the client for a certificate

## Checking Out The Certificate

* First, check to make sure the current time is between the "not before" time and "not after" time described in the certificate
* Next, Amazon.com's certificate was signed by "VeriSign Class 3 Secure Server CA"
* Can tell that Amazon.com's certificate was signed by the correct private key because the public key decryption is properly formatted
* Can also verify that the "VeriSign Class 3 Secure Server CA"'s certificate was signed by VeriSign's "Class 3 Public Primary Certification Authority"
  * This is last step in the trust chain - so how to trust this?
  * It's because it's an implicitly trusted good certificate and was introduced in September of 2000
  * At some point, must implicitly trust somebody - and so implicitly trusting that Robert Relyea made a good choice by adding "VeriSign Class 3 Public Primary Certification Authority" to implicitly trusted certificate
* Finally, check that the host name on the certificate is the one that is expected
  * Implicitly trusting people on the certificate trust chain to not do something bad like sign a certificate claiming to be from Amazon.com unless they were actually from Amazon.com

## Deriving the Master Secret

* The client generates a "pre-master secret" which is a random value that is sent to the server to generate a master secret
* This is done so that messages sent between the client and server are encrypted so that snooping cannot read sensitive information
* The client then encrypts the pre-master secret with the server's RSA public key (from the certificate)
  * What the server decrypts using the private key is equal to what the client encrypted
* The server calculates the master secret by using a pseudo-random function that combines the pre-master secret, the string "master secret", and the concatentated random values that were sent in the Client Hello and Server Hello messages
  * The pseudo-random function uses HMAC versions of MD5 and SHA1 but half of the input is sent to each hash function
  * Since the client knows the pre-master secret, and the random bytes that were sent to the server and from the server, it can calculate the master secret

## Finished Message

* The last handshake message the client sends out is the "Finished message"
* The client takes all bytes from all handshake messages and puts them into a "handshake_messages" buffer
* Uses the master secret, the pseudo-random function, and a hash of the "handshake_messages" buffer to generate a short set of bytes ("verify_data") that is used to ensure that the handshake has not been tampered with
* A sequence number is also included as part of the message data to foil attackers who might try to take a previously encrypted message and insert it midstream
  * In such a case, the sequence numbers would be different than expected

