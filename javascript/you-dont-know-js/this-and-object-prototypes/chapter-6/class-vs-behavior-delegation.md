# Classes vs. Behavior Delegation

* Tasks that have two types `XYZ` and `ABC`
* Here's class-based approach

```javascript
class Task {
	id;

	// constructor `Task()`
	Task(ID) { id = ID; }
	outputTask() { output( id ); }
}

class XYZ inherits Task {
	label;

	// constructor `XYZ()`
	XYZ(ID,Label) { super( ID ); label = Label; }
	outputTask() { super(); output( label ); }
}

class ABC inherits Task {
	// ...
}
```

* Here's behavior delegation approach
  * `Task` object has concrete utility methods that both `ABC` and `XYZ` might use
  * Create objects for `ABC` and `XYZ` that will delegate to the `Task` object when necessary
  * Think about performing task `ABC` as needing a combination of two peers - `ABC` object and `Task` object
* In the following example, `XYZ` is prototype-linked to `Task` and delegates to `Task`
* Kyle Simpson calls this `Objects-Linked-To-Other-Objects` or (`OLOO`)

```javascript
var Task = {
	setID: function(ID) { this.id = ID; },
	outputID: function() { console.log( this.id ); }
};

// make `XYZ` delegate to `Task`
var XYZ = Object.create( Task );

XYZ.prepareTask = function(ID,Label) {
	this.setID( ID );
	this.label = Label;
};

XYZ.outputTaskDetails = function() {
	this.outputID();
	console.log( this.label );
};

// ABC = Object.create( Task );
// ABC ... = ...
```

* Behavior-delegation avoids naming overwriting / shadowing properties (to avoid collisions)
  * This can make it easier to understand code as it leads to more descriptive names that are specific to the type of behavior each object is doing
* `XYZ` can use `Task` methods inside it's own methods since it delegates
* Rather than organizing objects mentally in a vertical fashion with Parents flowing down to Children, think of objects side-by-side, as peers, with delegation links between objects

## Another example

* The first is an `OO` approach while the second is an `OLOO` approach

```javascript
function Foo(who) {
  this.me = who;
}

Foo.prototype.identify = function() {
  return 'I am ' + this.me;
}

function Bar(who) {
  Foo.call(this, who);
}

Bar.prototype = Object.create(Foo.prototype);

Bar.prototype.speak = function() {
  return 'I say ' + this.me;
}

const bar1 = new Bar('jae');
const bar2 = new Bar('baebae');

bar1.speak();
bar2.speak();
```

```javascript
const Foo = {
  init: function(who) {
    this.me = who;
  },
  identify: function() {
    return 'I am ' + this.me;
  }
}

const Bar = Object.create(Foo);

Bar.speak = function() {
  return 'I say ' + this.me;
}

const bar1 = Object.create(Bar)
bar1.init('jae');
const bar2 = Object.create(Bar)
bar2.init('baebae');

bar1.speak();
bar2.speak();
```

* `OLOO` approach is slightly less confusing because it doesn't have to deal with `new`, constructors, or prototypes, for example
* Might have noticed that `new Bar() => Object.create(Bar)` followed by `barInstance.init` call
  * Some argue that this is adding more cruft
  * One benefit is that class constructors essentially force you to instantiate an object and also initialize it
  * In this case, those two things can be separated
  * A more specific example is a pool of objects that are only initialized with values when they're needed
