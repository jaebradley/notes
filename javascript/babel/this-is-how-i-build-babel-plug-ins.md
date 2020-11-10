# [This Is How I Build Babel Plug-Ins](https://medium.com/the-guild/this-is-how-i-build-babel-plug-ins-b0a13dcd0352)

* Goal is to take the following

```javascript
foo = 'foo'
```

* And output the following

```javascript
if (typeof window.foo === 'undefined') window.foo = 'foo'
```

* The parser parses the code string into an abstract syntax tree (`@babel/parser`)
* The AST is manipulated by plug-ins which use `@babel/traverse`
* The AST is transformed back into code using `@babel/generator`
* The `visitor` property is where the actual manipulation of the AST is done
  * The `visitor` walks through the tree and runs handlers for each specified node type
  * So in the following transformation logic, when the `visitor` encounters a node of type `AssignmentExpression`, it replaces the right operand with `bar` in the case where `foo` was assigned to the `foo` variable

```javascript
visitor: {
  AssignmentExpression(path) {
    if (
      path.node.left.type === 'Identifier' &&
      path.node.left.name === 'foo' &&
      path.node.right.type === 'Literal' &&
      path.node.right.value === 'foo'
    ) {
      path.node.right.value = 'bar'
    }
  }
}
```

* However, this transformation logic does not accomplish the desired outputted behavior - still need to incorporate the idea of `window`

```javascript
module.exports = ({ types: t }) => {
  return {
    visitor: {
      AssignmentExpression(path) {
        if (
          path.node.left.type === 'Identifier' &&
          !path.scope.hasBinding(path.node.left.name)
        ) {
          path.node.left = t.memberExpression(t.identifier('window'), t.identifier(path.node.left.name))
        }
      }
    }
  }
}
```

* The `types` object is a library for AST nodes that provides many utilities
  * Its methods should all be equivalent to camel-cased node types
* The `scope` object contains utilities which are related to the current node's scope
  * It can check whether a variable is defined or not, generate unique variable IDs, or rename variables
  * In the transformer logic, the `hasBinding` method is used to check whether the identifier has a corresponding declared variable or not by climbing the AST
* So the above translates `foo = 'foo'` to `window.foo = 'foo'` - but still need to transform assignment expressions into conditional assignment expressions (i.e. the `if (typeof etc etc`)
* `typeof window.foo` is a `UnaryExpression`, `=== 'undefined'` is a `BinaryExpression`, and `if(...)` is an `IfStatement` - all of these are new node types
* In the transformer logic, the different nodes use the node that was created / generated before it

```javascript
module.exports = ({ types: t }) => {
  return {
    visitor: {
      AssignmentExpression(path) {
        if (
          path.node.left.type === 'Identifier' &&
          !path.scope.hasBinding(path.node.left.name)
        ) {
          path.node.left = t.memberExpression(t.identifier('window'), t.identifier(path.node.left.name))
        }
        if (
          path.node.left.type == 'MemberExpression' &&
          path.node.left.object.name == 'window'
        ) {
          const typeofNode = t.unaryExpression('typeof', path.node.left)
          const isNodeUndefined = t.binaryExpression('===', typeofNode, t.stringLiteral('undefined'))
          const ifNodeUndefined = t.ifStatement(isNodeUndefined, t.expressionStatement(path.node))

          path.replaceWith(ifNodeUndefined)
          path.skip()
        }
      }
    }
  }
}
```
