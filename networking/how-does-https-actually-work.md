# [How does https actually work](https://robertheaton.com/2014/03/27/how-does-https-actually-work/)

* `HTTPS` is just `HTTP` protocol with `SSL/TLS` encryption layer
  * Client and server still communicate using `HTTP` just using a secure SSL connection that decrypts and encryptes requests and responses
  * Anybody can intercept messages, but they cannot read any of the data that's being sent
* SSL connection
  * Hello
    * Client sends a "hello" message providing server all information needed to connect to client including various cipher suites and maximum SSL version that it supports
    * Server responds with the cipher suite and SSL version to use given client's information
  * Certificate Exchange
    * Connection is established, server needs to provide identity to client
    * SSL certificate contains metadata (like owner name)
    * Client checks if it implicitly trusts certificate of if certicate is verified and trusted by one of many Certificate Authorities
  * Key Exchange
    * Client provides key used to encrypt messages between client and server (i.e. symmetric algorithm)
    * Client and server need to agree on key, which is established using asymmetric algorithm (public/private key)
    * Client generates a random key to be used for main message encryption
    * Client encrypts this random key using algorithm agreed-upon in Hello phase
    * Client further encrypts using server's public key (which is in SSL certificate)
    * Client sends server this encrypted key
    * Server decrypts value using private key
* Certificate Trust
  * If certificate is on list of certificates that are implicitly trusted
    * Browsers come with pre-installed list of trusted SSL certificates
  * Digital Signature
    * Used to verify that when server says "I'm Microsoft" they're actually Microsoft
    * SSL certificates can be "signed" by a Certificate Authority
      * Basically means that authority says that controller of certificate also controllers the domain on the certificate
      * Authority uses private key to encrypt certificate, and this encrypted text is attached to certificate as digital signature
      * Anybody can decrypt signature using authority's public key and verify expected decrypted value
      * Only authority can create valid signature
    * In Microsoft case, use CA's public key to decrypt SSL certificate and check that signature is valid
    * A self-signed certificate means that digital signature was generated using certificate's private key
  * It's not about trusting the party that sent certificate, but trusting _public key in certificate_
    * Any server could copy an SSL certificate and pretend to be Microsoft
    * Client would accept SSL certificate
    * But only Microsoft would be able to decrypt key in the Key Exchange because only Microsoft would have private key necessary

