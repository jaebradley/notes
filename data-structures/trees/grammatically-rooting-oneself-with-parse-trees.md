# [Grammatically Rooting Oneself With Parse Trees](https://medium.com/basecs/grammatically-rooting-oneself-with-parse-trees-ec9daeda7dad)

* A parse tree is an illustrated, pictorial version of the grammatical structure of a sentence
* In the noun phrase, "the pie", the **determiner** is `"the"`
* `2 x 8` - three distinct pieces in the expression
  * An expression on the left, an expression on the right, and the multiplication operator that combines both expressions on either side together
* For slightly more complicated expressions like `5 + 1 x 12` there are multiple parse trees that can be created to represent this expression (i.e. the grammar is ambiguous)
  * It can also imply that two totally different values can result from the same expression being represented / interpreted in two different ways
* So need to strictly define grammatical rules (like PEMDAS, in the previous case) so that compiler knows how to interpret expressions
  * For example, most programming languages have a grammar that enforces precedence so some operations / symbols have a higher weight than others
  * Another way to combat ambiguity is to enforce things like reading expressions from left-to-right (left associativity)
* Terminals or tokens are things like operation signs, parenthesis, reserved conditional words (like `if`, `else`)
  * They are symbols that cannot be broken down any further
  * They are the "leaves" in the parse tree
  * Don't have to be special characters but can also be numbers, strings, etc.
* Non-terminals are expressions, terms, etc. that can be broken down further (i.e. are non-leaf nodes in the parse tree)
* A parser must be able to generate a single non-ambiguous parse tree (given a valid sequence of tokens) following the syntax of the language
  * Must also be able to inform and detect when syntax errors are identified
* For mathematical expressions, the parser would need to ensure that the tree is evaluated from left to right, with operators of the same precedence
  * And that when the tree is traversed using inorder traversal from the bottom, that no syntax error occurs
