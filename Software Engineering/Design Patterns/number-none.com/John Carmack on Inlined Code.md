# [John Carmack on Inlined Code](http://number-none.com/blow/john_carmack_on_inlined_code.html)
* 'An exercise that I try to do every once in a while is to "step a frame" in the game, starting at some major point like common->Frame(), game->Frame(), or renderer->EndFrame(), and step into every function to try and walk the complete code coverage.'
* Inlining functions has the the benefit of not making it possible to call the function from other places
  * As a codebase grows over the years, there are opportunities to take a shortcut and call a function that does only the work you think needs to be done
  * There's a `FullUpdate` function that calls `PartialUpdateA` and `PartialUpdateB`
    * You may think you only need to call `PartialUpdateB` and are being efficient by avoiding calling `PartialUpdateA`, but bugs can stem from the execution state not being exactly what you expect it to be
  * The function that is least likely to cause a problem is the one that doesn't exist, which is a benefit to inlining
* If a function is called from multiple places, see if it is possible to arrange for the work to be done in a single place, with different variable arguments
* If there are multiple versions of a function, consider making a single function with more, possibly defaulted, parameters
* Minimize control flow complexity and "area under `if`s"
