# [Explaining Public-Key Cryptography to Non-Geeks](https://medium.com/@vrypan/explaining-public-key-cryptography-to-non-geeks-f0994b3c2d5)

* Symmetric cryptography is like a "regular" lock on a box
  * Only one key to lock / open it unless you get copy of key
* Asymmetric cryptography is a lock with three states
  * A (locked) <-> B (unlocked) <-> C (locked)
  * There are two keys - first can only go from A to B to C and second can only go from C to B to A
  * User keeps one key for themselves - this is their private key because only they have it
  * Second key is the public key - they are distributed to many people
* If you want to send user a personal document, you lock it using public key
  * This turns lock to position A
  * Can only be unlocked (i.e. in position B) by using private key, which only user has
* Can use asymmetric cryptography to verify that a particular user signed a document
  * If a user signed a document with their private key, only their public key could "unlock" it
