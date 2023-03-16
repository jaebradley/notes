# [WTF Is The Blockchain](https://medium.com/hackernoon/wtf-is-the-blockchain-1da89ba19348)

* Everybody has an empty folder - individuals add pages to their folders
  * Set of pages in the folder forms register to track transactions
* To make the transaction, individual #2 shouts and tells everyone, "I am transferring $10 to individual #9. So everyone please make a note of that in your pages"
* At a certain point, the current page runs out of space to log transactions
  * Put page in the folder and bring out new page and repeat the process of tracking transactions in the new page

## Putting Away The Page

* Before we put the page in our folders, we need to seal it with a unique key that everyone in the network agrees upon
* By sealing it, we will make sure that no one can make any changes to it once its copies have been put away in everyone's folder
* Once in the folder, it will always stay in the folder - sealed
* If everybody trusts the seal, everyone trusts the contents of the page
* Sealing of the page uses a hash function, which, given an output, it is extremely difficult to calculate the input, but given the input and the output, it is pretty easy to verify if the input leads to the output
  * Example in the article is that given a box containing the number 20893, find a number, when added to 20893, and then hashed by the hash function, generates a word that starts with three leading zeroes
  * Need to try all numbers in the entire universe until eventually get to a number like 21191
  * 21191 becomes the seal for the number 20893
  * If anybody wants to verify whether the page was altered, all they would have to do is add the contents of the page with the sealing number and feed it to the hash function
  * If the machine returns a word that starts with three leading zeroes then the contents were untouched
  * Otherwise, the page has been compromised
* Everyone in the network tries to calculate the sealing number for the page (after it has been filled with transactions)
  * The first one in the network to figure out the sealing number announces it to everyone else
  * Once the sealing number is heard, everyone verifies it and labels their pages with this number
* If somebody has a contradictoary sealing number, that person has only one choice, to discard their page and copy it from someone else so that they too can put it in the folder
  * If they do not put their page in the folder, they cannot continue writing further transactions / can't be part of the network
* The first one to calculate the sealing number gets rewarded with free money for their efforts (CPU power + electricity)
  * People are awarded Bitcoin for calculating sealing numbers
* Every page depends on its previous page - if somebody were to modify a historical page, they would also have to change the contents and the sealing number of all the pages after that, to keep the chain consistent
* What happens is that if one person cheats, they would create another chain in the network, but that chain would never be able to catch up to the honest chain, because that one person's effort and speed cannot beat the cumulative efforts and speed of the nine others (assuming a organization of 10 people)
* The longest chain in the network is the honest chain
* If the majority of individuals decide to be dishonest and cheat, the protocol will fail (called the 51% attack) - blockchain is built on the assumption that the majority of a crowd is always honest
