# [Ethereum Accounts](https://ethereum.org/en/developers/docs/accounts/)

* An account is an entity with an ether balance that can send transactions on Ethereum
  * Accounts can be user-controlled or deployed as smart contracts
* Two types of accounts, externally-owned accounts (controlled by anyone with the private keys) and contract accounts (a smart contract deployed to the network)
  * Both accounts have the ability to receive, hold, and send ETH
  * Both accounts can interact with deployed smart contracts
* Externally-owned accounts cost nothing
  * Can initiate transactions
  * Transactions between externally-owned accounts can only be ETH transfers
* Contract accounts cost something because they use network storage
  * Can only send transactions in response to a transaction
  * Transactions from an external account to a contract account can trigger code which can execute many different actions, such as transferring tokens or even creating a new contract

## Account Fields

* Nonce - counter that indicates number of transactions sent from the account
  * This ensures transactions are only processed once
  * in a contract account, this number represents the number of contracts created by the account
* balance - number of Wei owned by the account - Wei is denomination of ETH
* codeHash - For contract accounts, this is the code that gets hashed and stored as the `codeHash`
* storageRoot - 256-bit hash of the root node of the Merkle Patricia trie that encodes the storage contents of the account
