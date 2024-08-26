# [All I Know About Certificates -- Certificate Authority](https://www.pixelstech.net/article/1722045726-All-I-Know-About-Certificates----Certificate-Authority)

* The role of the certificate is that the only holder of the certificate should be the real entity
* Common misconception is that only certificate authorities can issue certificates - anyone can issue certificates
* Certificates issued by authoritative certificate authorities should be trusted
* Client trusts certificate authority
* Certificate authority issues certificates to websites
* Client visits a website, website presents certificate, client trusts the certificate authority, thus it also trusts the website

## Certificate Authorities

* When someone applies for a certificate, the CA needs to verify the applicant’s identity. If they are not super-bank.com, then cannot issue a certificate for super-bank.com
* Protect private key of their root certificate
* Be trusted by clients

### Verifying Website Identity

* ACME challenge - prove to me you are super-bank.com by making the URL super-bank.com/well-known/acme-challenge/foo return the text “bar”
* Demonstrates control over the domain
* Could also be a DNS TXT record

### Use of Intermediate Certificates

* The CA root certificate usually does not directly issue credentials to websites
* It issues an intermediate certificate, which is then used to issue certificates to websites
* If a client trusts the CA, then the client should trust certificates issued by the CA, and should trust certificates issued by those certificates, etc
* CA-issued certificates have a field with the value `CA:FALSE`. Root and intermediate certificates have a `CA:TRUE` value

### Certificate Issuance Process

* Data encrypted with one key can only be decrypted with the other key
* One key is released as the public key, and the other is kept private
* Hash the entire certificate and encrypt the hash value with the private key
* Verifier hashes the entire certificate, uses the public key to decrypt the encrypted hashed value. If values match, then the private key holder performed the encryption

### Client Verification Process

* After the CA signs the certificate, nothing about the certificate can be modified, including `CA:FALSE` and the validity period
* An expired certificate must be re-signed - changing the date to extend its use is not possible because the encrypted hash value will no longer match the certificate value
* CA does not sign anything with the root certificate - CA only uses its private key to append an encrypted hash value to the original certificate

### Certificate Authorities Must Be Trusted by Clients

* A new CA can acquire trust by having a single root CA sign the new CA’s root certificate
