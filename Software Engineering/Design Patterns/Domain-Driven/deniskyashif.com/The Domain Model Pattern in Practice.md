# [The Domain Model Pattern in Practice](https://deniskyashif.com/2026/04/14/the-domain-model-pattern-in-practice/)
* Simplified banking account example that has a `TransferService` with a `Transfer` method which has a bunch of validation (is the account frozen, does the account have a non-zero amount, etc) before transferring money from one account to another
* Author calls this procedural logic a "transaction script"
* When business complexity grows, these transaction scripts don't scale well to match that complexity
* If there are cross-cutting business rules like "a frozen account cannot perform _any_ money-related operations", every transaction script would need to enforce this check independently
  * And then when the rule changes to "frozen accounts may receive money but not send it", every place that the previous rule was applied needs to be updated
* Anemic domain models are objects that carry data but do not have any behavior where all the logic is pushed to the service layer

  ## Implementing the Domain Model Pattern
  * A domain model encapsulates both data and behavior in the same objects and are where business invariants live and are consistently enforced
  * Refactored examples now have `Money` and `BankAccount` records that support behavior like `Add`ing and `Subtract`ing
  * There is a `TransferMoneyHandler` class that is the "application" layer and handles orchestration only i.e. committing data to a database
