# [Lies We Tell Ourselves About Email Addresses](https://gitpush--force.com/commits/2026/06/lies-we-tell-ourselves-about-email/)
* Verify the address, and don't validate it
  * Send an email, let the user click a verification link or input a verification code
  * Don't check the domain's MX record, the underlying email service does this
* Email providers may support invalid email addresses
  * RFCs that have a local-part restrictions: "The maximum total length of a user name or other local-part is 64 octets."
  * `local-part` is everything before the `@` character
  * Example invalid email address (`entirelytoomanycharactersinthisemailwhatisevenhappeningblahblahdonttrythisathome@gitpush--force.com`) is able to receive email
* After 2012, the `SMTPUTF8 extension` allows for non-ASCII characters in the local-part of email addresses
* Local part is defined as an octet stream, so bytes that don't map to valid characters in standard unicode can be present
* Email addresses don't _always_ have a second-level domain and a top-level domain
  * `co.uk`, `co.au`, etc
  * Email addresses can include an IP address instead of a domain name: `abc@[some ip address]`
    * Author says Gmail's web client disallows these email addresses
* Author has an email address with a top level domain that is `.email`
  * Author created an alias with a `.net` domain because many companies have validation logic that doesn't allow domains other than the "normal" domains (`.com`, `.net`, `.org`, etc)
* Email addresses _can_ end in a dot
  * `.` represents the root zone
  * So `localpart@gmail.com` and `localpart@gmail.com.` will be the same mailbox
  * `RFC 5332` disallows trailing dots - another example of RFC violations that work in-practice
* Forcing email addresses into a single case can be destructive
  * > If my name was Ben Weiß and my email address was benweiß@gitpush–force.com, you could run into problems if you tried to force it to uppercase, because the non-ASCII ß folds up not only into a different letter, but TWO letters, two ASCII characters at that: SS.
  * Forcing to lowercase doesn't solve this either as there are similar examples (the Turkish İ folds down into a normal ASCII i)
  * The author suggests the following solution at the database level: "use citext in Postgres, or COLLATE utf8mb4_general_ci in MySQL."
* Author has an email address pattern of using a plus tag when specifying an email for a specific company's website like `username+company@{emailhost}.{domain}`
