# [How Does A Random Salt Work](https://security.stackexchange.com/questions/66989/how-does-a-random-salt-work)

* To verify the hashed password without a salt, compute the MD5 hash (or use a _better_ hash function) of the provided password with data stored in the database
* If a salt is used, then the hash is of the provided password + salt
  * Store the salt in the database with the password (both on the user)
* If no salt is used, then if the hacker is able to identify one password in the table then they know what the hash function is and can decode all the other passwords
* If the same salt is used for each salt value, then brute-forcing passwords will take more time but the same vulnerability remains - if a single password is identified (and thus, the global salt is identified) then all passwords can be identified
* If a random salt is used for each password then the hacker would need to bruteforce every single password which makes the security of the stored password much higher
