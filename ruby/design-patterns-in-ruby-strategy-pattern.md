# [Design Patterns in Ruby: Strategy Patterns](https://medium.com/rubyinside/design-patterns-in-ruby-strategy-pattern-17e2fa191d9c)

* Warden uses strategy pattern by keeping authentication algorithms separate from code that performs the authentication
  * New algorithms can be employed without modifying Warden
  * Warden can choose a winning strategy at runtime without knowledge of how the algorithm is implemented
* `Warden::Strategies::Base` provides a common abstract interface for all strategies to inherit from
  * Each strategy needs to implement its own `authenticate!` method and then Warden takes care of how the strategy is used
* Warden iterates through strategies in the `run_strategies_for` method and determines if any of them are valid
  * The benefit of having Warden iterate through strategies is that if code had to implement strategies as well, it would be much more convoluted, especially when adding a new strategy
