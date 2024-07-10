# Chapter 1: Analyzing Business Domains

## What Is a Business Domain?

* Business domain defines a company's main area of activity - the service they provide to clients
  * FedEx = courier delivery
* Company can operate in multiple business domains and change domains

## What Is a Subdomain?

* Subdomain is a fine-grained area of business activity
* All of a business' subdomains form its business domain
* Starbucks' main business domain is coffee but example subdomains include buying / renting real estate, hiring, financial management
  * None of the subdomains on their own make a profitable company - all of them are necessary for Starbucks to be able to compete
* Core subdomains are what a company does differently from competitors - inventing new products or services, reducing costs by optimizing existing processes
* Core subdomains tend to be naturally complex
  * Subdomains that are simple to implement provide a short-lived competitive advantage
* Generic subdomains are business activities that all companies perform in the same way
  * Generally complex and hard to implement
  * Generic subdomains do not provide any competitive edge to a company - battle-tested implementations are widely available and every company uses them
  * Example is auth - instead of investing in a proprietary authentication mechanism it makes more sense to use an existing solution
* Supporting subdomains do not provide a competitive advantage
  * Example is advertising company whose main core subdomain is matching ads to visitors, optimizing ads, etc.
  * Author contends that the way the company stores and indexes its physical creative materials does not impact its profits (I disagree)
  * Author posits the content cataloging solution as one of the company's supporting subdomains

## Comparing Subdomains

* Supporting subdomains have low entry barriers and don't provide a competitive advantage
  * Companies wouldn't mind if a competitor copied its supporting subdomains
* Challenging to differentiate between core subdomain and supporting subdomain
  * Ask whether the subdomain could be turned into a side business - if so, then it is a core subdomain
  * If its easier to create your own implementation rather than integrating with an external one than this is a supporting subdomain
* Business logic resembling CRUD interfaces = supporting subdomain
* Complex algorithms or business processes = core subdomain

## Identifying Subdomains

* Company's departments and organizational units
  * Online retail store has warehouses, customer service, picking, shipping, etc
* Example of customer service department
  * Help desk system, shift management, scheduling, telephone system, etc
  * Company developed algorithm for routing incidents to agents
    * Algorithm analyzes incoming cases and past incident similarities and is nontrivial
    * This algorithm is a core subdomain as it is a "competitive advantage"
* In contrast, the help desk and telephony subdomains are generic as they are outsourced
* Technically, subdomains are sets of interrelated, coherent use cases
  * Usually the use cases involve the same actor, business entities, and all manipulate closely related sets of data
* When a subdomain's set of coherent use cases has been identified, can stop looking for finer-grained subdomains

## Gigamaster

* Ticket sales and distribution company
* Core subdomains are its recommendation engine, privacy / data anonymity, mobile application experience
* Generic subdomains are encryption, accounting (company is in the sales business), charging customers, authentication and authorization for identifying users
* Supporting subdomains are integrating with music streaming services, integrating with social networks, logic around incorporating attended concerts
