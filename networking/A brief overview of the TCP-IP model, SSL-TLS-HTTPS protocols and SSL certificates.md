# [A brief overview of the TCP/IP model, SSL/TLS/HTTPS protocols and SSL certificates](https://medium.com/jspoint/a-brief-overview-of-the-tcp-ip-model-ssl-tls-https-protocols-and-ssl-certificates-d5a6269fe29e)

## TCP/IP Communication Model

* The application layer gets data from a source, and wraps some headers of a particular protocol like HTTP
  * This creates a data package of the HTTP protocol and can be read by an application that can understand it, like a web browser
* The HTTP package is sent to the transport layer, which wraps the package with TCP protocol headers, along with the source and destination ports
  * Source port is the port of the application transmitting the data, like a web browser, and the destination port is where the application running on the receiving side will receive it
* The TCP package is received by the internet layer, which wraps some IP protocol headers with source and destination IP addresses
  * The source IP address is the IP address of the sender device, while the destination address is the IP address of the receiver device
  * Once the package is stamped with the IP protocol, it becomes a network package that is good enough to be transmitted over the internet
* This network package can be received and peeled like an onion until the original data is recovered
  * This data is extracted one layer at a time, by looking at the protocol of that layer

## HTTP Protocol

* Before any data is sent to the receiver, a communication channel is opened between the sender and the receiver
  * This is done using TCP/IP alone, without any application layer
* First, the client sends an empty packet to the sender with the TCP protocol `SYN` flag set to `1`
  * When this packet is received by the server, it knows that a client is trying to establish a connection / session
* The server sends back an empty package with `SYN` and `ACK` flags set to `1`
  * When the client receives this packet, it knows that the server has responded and is willing to accept the request
* Then the client responds with an empty package with `ACK` set to `1`
  * Once the server recives this package, a TCP communication channel is opened
* Once a server receives an HTTP GET request from a client, a server can acknowledge the request by sending data back to the client
* Once the server has sent all the data back, in one or multiple packets, the client has to acknowledge receipt by sending empty packages that have `ACK` flags set to `1` and the sequence numbers of the packets it is sending acknoledgement of
* Once the server has no more data to send, it will send an empty packet with the `FIN` flag set to `1` to indicate the finished message
* There could be a loss of packets, and packets may arrive in the wrong order, hence such packets need to be retransmitted again
* In HTTP persistent connection, the same TCP connection will be used to request other resources - this is more efficient because we don't have to go through the same TCP three-way handshake again and again
* HTTP is an insecure protocol since data in an HTTP protocol is encoded in plain text format
  * Any man-in-the-middle can listen to TCP communication and read your personal data transmitted over the web

## Overview of SSL/TLS

* The role of the TLS layer is to establish a secure connection with the server using a TLS handshake, after the TCP handshake and encrypt the HTTP data using some encryption algorithms negotiated with the server
* The final encrypted data becomes the application data of the network packet the server is going to receive
* Since the application data is encrypted, any man-in-the-middle attack might obtain the data, but won't be able to make sense of it

### Asymmetric Key Algorithm

* A public key is used to encrypt the data and made available to the public. Only the private key can decrypt the data.
* Asymmetric key algorithms are generally slower and CPU-intensive

### Symmetric Key Algorithm

* The same key is used to encrypt and decrypt data
* The symmetric key algorithm is mainly used to open an encryption channel between two trusted parties
* Only these two parties will ever know about the data being shared between them since nobody else on the network has access to the shared symmetric key

## HTTPS Handshake with TLS 1.2

* A TCP connection is established using a three-way handhake we saw before (client asks to sync, server acknowledges sync, client acknowledges)
* After a TCP connection is established, the client sends an empty packet with a Client Hello message
  * Client Hello handshake message has cipher suites attached to the application
  * Also sends a server name indication message that indicates the hostname of the service we are trying to connect with
    * This is used by server to send back an appropriate SSL certificate
* Server sends back a Server Hello message after reciving Client Hello
  * Packet contains the cipher suite the server has chosen from the list of choices the client provided
* It then sends a packet containing the SSL certificate for the domain name that was requested
* Once the cipher suite is agreed upon, encrypted communication can start
* So far, all our packets were not encrypted as they did not contain any application data and had no prior knowledge of encryption
* The client now generates a shared secret key for bulk data encryption
  * It will create a 256-bit key of the AES symmetric-key algorithm
  * This key needs to be shared with the server
* Using the RSA public key of the server obtained from the SSL certificate, the client will encrypt the shared key and send it to the server with the message Client Key Exchange
* Once the server receives this packet, it will receive the shared symmetric-key by decrypting the data using its RSA private key
  * Since nobody else has access to the private key, this shared secret key can not be read on the network

## Data Integrity over TLS

* A digital signature is nothing but data encrypted with a private key that can be decrypted with the public key
* The client can verify this digital signature using the server's public key and if it fails, the TCP connection is dropped
* The server first computes the hash of the data with a hashing function like SHA and encrypts it with the private key
  * This encrypted hash is the digital signature / message authentication code
* The client decrypts the encrypted payload using the symmetric key algorithm, extracting the data and the digital signature
* It checks if the digital signature is actually signed by the server using the server's public key and extracts the hash value
* Then the client computes its own hash using the same hashing function of the data received from the server
  * If the hashes match, the data is secure
