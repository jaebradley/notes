# [The Missing Manual for Signals: State Management for Python Developers](https://bui.app/the-missing-manual-for-signals-state-management-for-python-developers/)

## The Problem with Traditional State Management
* Example code executes a variety of different actions on adding an order, like
  * Appending the order to the current list of orders
  * Updating derived values like total revenue, average order value (which depends on total revenue, so the order in which these operations on executed matters)
  * Side-effects like sending notifications and analytics
* These dependencies are implicit and manually maintained
* This leads to tight coupling - and missing to implement a tightly coupled data update leads to silent bugs
* Developers must trace through all the method calls to understand what depends on what
* Opportunity for race conditions - for example, between when an order is added to the current list of orders and the calculation of total revenue

## What Are Signals, Really?
* Dependency graph abstraction that inverts the control flow of state management
* They are value containers, and not event streams
* Signals hold current state and create a snapshot of your application at any point in time
* Event listeners create reaction patterns - responses to things happening over time
* Push-based updates (imperative) have a pattern like
  * X changes
  * Manually update Y
  * Manually update Z
  * Manually notify observers
  * "When this happens, do these things in this order"
* Signals Approach (pull-based)
  * Y is computed from X
  * Z is computed from X + Y
  * Effect observes X, Y, Z
  * So then when X.set(new value) is called, this effect will execute
  * "These relationships always hold true"
