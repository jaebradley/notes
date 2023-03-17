# [RSA Algorithm](https://www.cs.colorado.edu/~srirams/courses/csci2824-spr14/rsa-13.html)

* Theoretically, it would be great if Alice sends Bob some message that she hopes only Bob can read
  * She encrypts the message using Bob's public key and then Bob uses his private key to decrypt it - only somebody with Bob's private key should be able to decrypt the message
  * Need to create some "one-way function" such that it's computationally hard to invert the one-way function and compute the original message
  * However, needs to be computationally easy to compute the original message from Bob's private key
* RSA uses really large number, `n` where the public key is `e` and the private key is `d`
* The generated cipher is computed by raising the message, `M` to the power of `e` and taking the modulo `n`
  * `M^e mod n` - this is moular exponentiation and is relatively computationally inexpensive
* However, given the public key, `e`, and the cipher / generated value, it's very difficult to generate the original message
* Now, need to find some private key, `d` such that the cipher value, raised to the power of `d` modulo `n` returns the original message
  * `c^d mod n = M`
  * This implies the equation `(M^e)^d mod n = M nod n`
  * Need to find a pair of `e` and `d` such that it satisfies the relationship and given `e` + `n` it's hard to identify `d`

## Totients

* The totient of a number is the total number of integers between `1` and `n` - 1 that are relatively prime with `n`
  * Relatively prime means no prime factors are shared (like `10` and `21` even though both are _not_ prime themselves)
* The totient of a prime number is the value of the number, minus `1` (i.e. every number from `1` to `n` - 1 will be relatively prime with prime number `n`)
* Euler's theorem states that given two relatively prime numbers, one prime number, raised to the totient of the other prime number, modulo'd by the other prime number equals `1`
  * `first_prime^totient(second_prime) mod second_prime = 1`

## RSA Scheme

* Choose two different large prime numbers and generate their product - this is the large number `n` that was discussed earlier
* Derive some `k` that is the totient of `n` (which is really `first_prime` - 1 X `second_prime` - 1)
* Pick some number that is relatively prime to this totient value - this will be the public key
* Then compute a private key such that the product of the public key and the private key is equal to `1` + some number times the totient of `n` (which we called `k`)
  * `e X d = v X k + 1` (for some `v`)
