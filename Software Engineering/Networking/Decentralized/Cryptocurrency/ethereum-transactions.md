# [Transactions](https://ethereum.org/en/developers/docs/transactions/)

* Transactions, which change the state of the EVM, need to be broadcast to the whole network
  * Any node can broadcast a request for a transaction to be executed on the EVM
  * Transactions require a fee and must be mined to become valid

## LifeCycle

* Transaction is broadcast to the network and included in a pool with lots of other transactions
* A miner must pick your transaction and include it in a block in order to verify the transaction and consider it "successful"
  * You may end up waiting in this stage if the network is busy and miners aren't able to keep up
  * Miners will always prioritise transactions with higher GASPRICE because they get to keep the fees
* Transaction also gets block confirmation number
  * Number of blocks created since the block that your transaction was included in
  * The higher the number, the greater the certainty that the transaction was processed and recognised by the network
