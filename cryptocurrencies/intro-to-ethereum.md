# [Intro To Ethereum](https://ethereum.org/en/developers/docs/intro-to-ethereum/)

* A blockchain is best described as a public database that is updated and shared across many computers in a network
  * Block refers to the fact that data and state is stored in sequential batches or blocks
  * Chain refers to the fact that each block cryptographically references its parent
    * A block's data cannot be changed without changing all subsequent blocks, which would require the consensus of the entire network
* Anyone who wants to add new blocks to the chain must solve a difficult puzzle that you need a lot of computing power to work on
* New blocks are broadcast to the nodes in the network, checked and verified, updating the state for everyone

## What Is Ethereum?

* There is a single canonical computer, called the Ethereum Virtual Machine, whose state everyone on the Ethereum network agrees on
* Any participant can broadcast a request for this computer to perform arbitrary computation
  * Whenever such a request is broadcast, other participants on the network verify, validate, and carry out the computation
  * This causes state change in the EVM which is committed and propagated throughout the entire network

## What Is Ether?

* THe purpose of Ether is to allow for the existence of a market for computation
  * This market provides an economic incentive for participants to verify transaction requests and to provide computational resources to the network
* The amount of ether paid is a function of the length of the computation
  * Prevents malicious participants from intentionally clogging the network by requesting execution of infinite loops or resource-intense logic as these actors will be continually charged

## What Are DAPPS?

* Application developers upload programs into EVM storage, and then users make requests for the execution of these code snippets with varying parameters - these programs are called "smart contracts"

