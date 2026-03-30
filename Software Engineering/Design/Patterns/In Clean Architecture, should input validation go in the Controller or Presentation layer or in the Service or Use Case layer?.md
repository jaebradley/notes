# [In Clean Architecture, should input validation go in the Controller/Presentation layer or in the Service/Use Case layer?](https://www.reddit.com/r/softwarearchitecture/comments/1s45opa/in_clean_architecture_should_input_validation_go/)
* If the validation requires domain knowledge, it should be done in the domain
* If the validation does _not_ require domain knowledge, it probably indicates a presentation concern, and should be kept in the presentation layer
  * Potentially discardable when mapping from the presentation layer to the domain layer (like stripping dots and commas from a currency value)
* Domain should _not_ check the outer layers to check conditions that matter at the domain layer
* The domain defines some interface object (like a "price" object) that the presentation layer needs to provide
  * If this price object needs to have a minimum value, the domain layer needs to ensure that this business rule is met
