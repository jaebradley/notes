# [Naming 101: Programmer's Guide On How To Name Things](https://blog.elpassion.com/naming-101-quick-guide-on-how-to-name-things-a9bcbfd02f02)

* Variables should be named at one level of abstraction higher than its values - name needs to suggest purpose of variable but not possible values
* Example is variable to represent distance between two cities in kilometers or miles
  * Name could be `kilometers_or_miles` but same level of abstraction as the values it represents - if other types of units were ever going to be added (like feet) the name would not be correct
  * Instead, use a name like `unit` which is at one higher level of abstraction (i.e. more general) than the variable's responsibility
  * Now, need new method to measure distance between two cities - what should method be called?
  * Maybe it could be called `distance_between_city_x_and_city_y` but this is at the same level of abstraction as the returned values i.e. the name of the method suggests possible returned values. It's not particularly future-proof (what if different cities are added)
  * Name method `distance` - it's one level of abstraction higher than method body
* Naming classes should not anticipate future - should be based on contemporary assumptions
  * Don't expect car to acquire features of a boat
  * If current application requirements state that there is a need for a car, class name should be `Car` instead of `Vehicle`
