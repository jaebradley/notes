# [The Magic Behind One-Click Checkout: Understanding Idempotency](https://javarevisited.substack.com/p/the-magic-behind-one-click-checkout?r=thi9&utm_medium=ios&shareImageVariant=overlay&triedRedirect=true)
* Scenario: e-commerce checkout with payment information submitted, but a loading screen
  * User reloads checkout cart page and all items are still in the cart
  * Submitting payment information succeeds and charged a single time
* Idempotency key is unique for a particular cart
* Most persistence layers store data keyed by the idempotency key
  * If the key does not exist, persist the key, process the action (like checking out / payment processing), and store the result
  * If the key exists but the payload (value) is different, this is an error case
  * If the payload matches return the stored result
  * This approach prevents accidental duplicates and malicious replay attacks
* Redis is fast and great for low-latency checks
  * But not particularly fault-tolerant (lacks ACID guarantees) like a database
* Robust to use Redis for initial checks and write-through to a durable transactional database
* In this e-commerce cart scenario, if the cart changes (quantity, new item) then it should result in a new key
  * Keys need to be generated quickly, because lots of orders happen at the same time
* Order service seems like the natural place to generate idempotency keys as it has the most context around the business logic - that when a cart changes it should regenerate a key
* Idempotency key = hash(cart ID + cart state + amount)
  * Cart state is a serialized, sorted list of items + quantities
  * Amount is the total amount to charge, or some type of checksum to avoid mismatched retries with different amounts
  * Because the key encodes business logic, if the cart model changes, the key generation logic must evolve carefully
* Random keys (UUID per checkout attempt and stored with the order ID) require that the UUID is durably generated and stored as part of the order database creation
  * Key is no longer derived from business logic
  * Must atomically create the order record and generate + store the random UUID key _before_ calling the payment service or else retrying deterministically is not possible
