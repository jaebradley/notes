# [Explaining public-key cryptography to non-geeks](https://medium.com/@vrypan/explaining-public-key-cryptography-to-non-geeks-f0994b3c2d5)

* Symmetric cryptography
  * 1 key that encrypts and decrypts data

## Asymmetric cryptography

* Imagine a box with three states
  * `A` (Locked) <-> `B` (Unlocked) <-> `C` (Unlocked)
  * There are two keys - the first can only move from `A` to `C` and the second can only move from `C` to `A`
  * The first key is called a `private` key and the second is called a `public` key
* Imagine somebody wants to send information
  * They encrypt the information with the `public` key
  * Now it can only be decrypted using the `private` key (which hopefully only you have)
* It's important to note that the only way for information to move to state `C` is if it was *encrypted by the `private` key*
  * This means that the information was "digitally signed"
