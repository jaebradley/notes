# [What I Wish I Knew When I Became CTO](https://medium.com/sketchdeck-developer-blog/what-i-wish-i-knew-when-i-became-cto-fdc934b790e3)

* I now appreciate that the infrastructure, frameworks, and languages you choose will stick with you for a really long time (hence why cloud providers can offer $100,000 initial credit). As the company grows there is an incredibly strong pressure to build more features and sub-systems, each one further locking you into your choices.
* Of the list, AngularJS and MySQL have been the only ones to give us scaling problems. Our monolithic AngularJS code-bundle has got too big, and the initial download takes quite a while and the application is a bit too slow. MySQL crashes and restarts due to growing BI query complexity and it's been hard to fix this.
* However nicely or badly you write them, they'll remain in the same state - therefore it's good to invest a little more time than is comfortable building them well for the sake of future maintainers. "Try and leave this world a little better than you found it".
* I've found it a real struggle to get our team to adopt writing tests
* ...it never prepared me for how exhausting and excruciating hiring is
  * Only hire when you feel you're completely desperate for the role
  * Hire to keep up with growth, not to generate it
  * Don't hire someone to do something you've not yet figured out
* Your gut often knows you need to fire someone long before you rationally accept and perform it and it's always difficult.
