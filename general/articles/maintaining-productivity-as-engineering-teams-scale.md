# [Maintaining Productivity As Engineering Teams Scale](https://medium.com/accel-india-insights/maintaining-productivity-as-engineering-teams-scale-1a821f5add28)

* Three phases
  * Early stage (`< 6`): Teams do whatever it takes (quick and dirty)
  * Initial growth (`6 => 36`): Most early stage practices work
  * High growth (`> 36`): Every inefficiency gets multiplied by 36 or more
* Technical debt
  * Breaking product into small enough pieces means that teams can own each piece or module of product
  * "How do you eat a hippopotamus?" - "Piece by piece"
    * Never take up a rewrite of the entire product at once - comes with overly optimistic estimates
* Product debt
  * Bad feature decisions
  * Often comes from pleasing early customers / misunderstanding users
  * Features come with bugs and maintenance - take out features not adding business value
* How to deal with bugs
  * One option is to have separate team
    * Doesn't scale as team members won't be familiar with parts of code
    * Original owners end up being consulted by maintenance team, defeating purpose of removing distraction
  * Leave maintenance to original team
    * Will not be someone else's problem
  * To alleviate context switching, have allotted hours to time-box for hot fixes
* Invest in documentation
  * Two types
    * Transient, instructional - tell others what to do. Once done, they can be thrown away.
    * Permanent, referential - these are fundamental reference documents and rarely change. If they do change, you don't mind updating these documents.
  * Invest in the second kind of documentation
  * Always write down all the things you don't want to forget
    * This includes choices considered and why a particular decision was made
