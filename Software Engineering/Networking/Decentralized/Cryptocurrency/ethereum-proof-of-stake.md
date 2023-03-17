# [Proof-Of-Stake](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/)

* A type of consensus mechanism used by blockchain networks to achieve distributed consensus
* It requires users to stake their ETH to become a validator in the network
  * Validators are responsibile for the same thing as miners - ordering transactions and creating new blocks so that all nodes can agree on the state of the network
* Users will need to stake 32 ETH to beocome a validator
* Validators are chosen at random to create blocks and are responsible for checking and confirming blocks they don't create
* A user's stake is also a good way to incentivise good validator behavior
  * A user can lose a portion of their stake for things like going offline or their entire stake for deliberate collusion
* Validators don't need to use significant amounts of computational power because they're selected at random and aren't competing
  * They don't need to mine blocks, they just need to create blocks when chosen and validate proposed blocks when they are not
  * If you attest to malicious blocks, you lose your stake

## How Validation Works

### Attestation

* The attestation of another validator's proposal is recorded in the beacon chain rather than the transation itself
* At least 128 validators (the committee) are required to attest to each shard block
* The committee has a time-frame in which to propose and validate a shard block
  * Only one valid block is created per slot
  * There are 32 slots in an epoch
  * After each epoch, the committee is disbanded and reformed with different, random participants
  * This keeps shards same from committees of bad actors

### Crosslinks

* Once a new shard block proposal has enough attestations, a crosslink is created which confirms the inclusion of the block, and your transaction, in the beacon chain
* Once there's a crosslink, the validator who proposed the block gets their reward

