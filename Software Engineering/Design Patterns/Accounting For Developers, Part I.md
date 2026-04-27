# [Accounting For Developers, Part I](https://www.moderntreasury.com/journal/accounting-for-developers-part-i)

## Accounts

* An account is a segregated pool of value

## Transactions

* Atomic events that affect account balances
* Transactions are composed of entries
  * Transactions have at least two entries, each of which corresponds to an account
  * One entry represents the source, and the other entries represent the use of funds
* Suboptimal to mutate balances directly vs. computing a balance from a log of transactions
  * More accurate to store immutable transactions and compute balances from these transactions

## Dual Aspect

* What money was used for - debit normal accounts
* Where money came from - credit normal accounts

## Debits and credits

* Save for a few special situations, accounting systems only log positive numbers
* A debit entry will always increase debit normal accounts and decrease the balance in credit normal accounts (and vice-versa with credit entries) 
* There are transactions that may increase / decrease both debit normal and credit normal accounts
  * Company pays loans off
  * Cash decreases (decrease in debit normal account)
  * Outstanding loan amount decreases (decrease in credit normal account)
