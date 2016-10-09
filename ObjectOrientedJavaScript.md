# Object Oriented JavaScript

## Scopes
1. Lexical scope - describe regions in your source code, where you can refer to a variable by name without getting access errors.
1. Simple programs with no functions at all have only one scope: **Global Scope**
1. A new lexical scope is created everytime you type out a function definition:
```JavaScript
var x = function() {
	// some function

	var y = 1; // This variable cannot be access from the outside
}; 
// cannot access y from here.
```
1. JavaScript allows you to assign to variables you've never even declared before:
```JavaScript
var x = function() {
	// some function

	y = 1; // This variable will be added automatically to the global scope and not to whatever scope you did the assignment in. - this is bad practice though
}; 
// cannot access y from here.
```
1. Not all curly braces in JavaScript are relevant for scoping. Blocks on `if` statements or `while` statements and other looping constructs like that do not create new scopes. Only curly braces that you find on a function statement creates a scope.
1. Declare every variable with a `var` keyword.
1. When a program runs, it builds up storage systems for holding the variables and their values. These in-memory scope structures are called **execution contexts**.
1. Execution contexts differ from lexical scope in that they are built as the code runs, not as it's typed.
1. As the first line of code runs, the interpreter builds up a new key value mapping inside of the execution context, in order to keep track of the value.
1. The interpreter is going to ignore the content within the curly braces of a function until the function is called.
1. In-Memory Scopes vs. In-Memory Objects are not the same thing - they are kept completely separated by the interpreter. There are many limits on your access to an execution context that don't exist to your access to an object.
1. Many of the rules about object will also happen to be true for execution contexts, but cannot be mixed and matched. E.g. You will never be able to store an array full of contexts, eventhough you can do so with objects. You can iterate over the variable names in an execution context, the way you would over the keys in an object. So eventhough they are both key value data storage constructs, you're going to be interacting with the two in completely different ways.

## Closures
1. Every function should have access to all the variables from all the scopes that surround it. A closure is just any function that somehow remains available after those outer scopes have returned.
1. To retain a function objects (saga) after a function that created them (newSaga) had returned: 
	1. passing to setTimeout
	1. returning saga from newSaga
	1. saving saga to a global var
	```JavaScript
		var sagas = [];
		var hero = aHero();		
		var newSaga = function() {
			var foil = aFoil();
			var saga = function() {
				var deed = aDeed(); {
					log(hero + deed + foil);
				}
			};
		};
		newSaga();
		sagas[0](); // when this is invoked, although the function is stored in the global scope (sagas array), the execution context created should still be within the newSaga scope created.
	```
	[YouTube Link to Udacity Video](https://www.youtube.com/watch?v=-8kz4XByEDs)

## `this`
1. Parameters are words that we see between parenthesis in a function definition. There's two major differences between a regular parameter and the parameter this: 
	1. You don't get to pick the name for the parameter `this`.
	1. You go about binding values to the parameter `this` a bit differently from how you bind values to other parameters. There are five different ways.
1. `this` is an identifier that gets a value bound to it, much like a variable, but instead of identifying the values explicitly in your code, `this` gets  bound to the correct object automatically.
1. Mostly, the rules for how the interpreter determines what the correct binding is, resemble the rules for positional function parameters. The difference between positional function parameters and the keyword `this` are designed to support your intuition about which object should be the focal when you're invoking a method or a constructor.
```
	var fn = function(a, b) {
		log(this);
	};

	var ob2 = { method: fn}; // the keyword this doesn't refer to the object i.e. ob2

	var obj = {
		fn: function(a, b) {
			log(this);
		}
	}; 
	
	var ob2 = {method: obj.fn};
	// this is also NOT obj

	obj.fn(3,4) // the dot to the left of a function invocation, meaning it was looked up as a property of an object, the object that it was looked up upon, the object that the function is looked up upon when it's being invoked, that object is what this will be bound to.
```
1. `this` is **NOT** [(Youtube Link)](https://www.youtube.com/watch?v=ehZKcas9R-4):
	1. the function object created when the intrepreter hits it.
	1. the instance of that function is created - certain circumstances is true, but mostly not.
	1. in a function that is contained within some other object as a property
	1. the object created by the literal `this` appears within
	1. an "execution context" or "scope" of that function call
1. `this` is the object that the function is looked up upon when it's being invoked, that object is what `this` will be bound to.
	1. This is an approximate definition that is useful to get you out of 90% of situations because it may use brackets to access the function instead of the dot. Like this ```obj["fn"](3,4)```
1. There is no binding of parameters until a function is called.
```JavaScript
	var fn = function(one, two) {
		log(this, one, two);
	};
	var r = {}, g = {}, b = {}, y = {};
	r.method = fn; 
	r.method(g,b); // this is bound to r -- r{}, g{}, b{}
	fn(g,b); // this is bound to global -- <global>, g{}, b{}
	fn.call(r,g,b); // this is bound to r when call is used -- r{}, g{}, b{}
	r.method.call(y,g,b) // this is bound to y as .call overrides access rules -- y{}, g{}, b{}

	setTimeout(fn, 1000); // need to look at documentation of setTimeout
	// imagine setTimeout() looks something like this:
	var setTimeout() = function(cb, ms) {
		waitSomehow(ms);
		cb(); // since there is no object invoking the cb function (fn in this case), this is bound to global
	}
	// hence
	setTimeout(fn, 1000) // logs -- <global>, undefined, undefined

	// this time if you do this:
	setTimeout(r.method, 1000);  // still logs -- <global>, undefined, undefined because left of cb() is still the global scope.
``` 
1. The problem of losing parameter bindings is pretty widespread since any function like setTimeout that takes another function as a callback may actually call that function differently than you expected.
	1. Callback functions are inherently designed so that they will be invoked by the system you pass them to. Thus you generally have little control over what the bindings will be for the parameters of the functions that you passed in. For this reason you need to be careful about all the parameter bindings, including the parameter this.
1. One way to pass a callback without complicating the situation is to pass a different function - one that doesn't receive any parameters at all, including this, then just make room in the body of that function for your custom code and inside that area you reference your method and you can do the indication yourself. Like this:
```JavaScript
	setTimeout(function() {
		r.method(g,b);	// this logs -- r{}, g{}, b{}
	}, 1000);

	new r.method(g,b); // this logs -- newObj{}, g{}, b{}
``` 
1. **IMPORTANT**: The keyword this makes it possible for it to build just one function object and use that as a method on a bunch of other objects. Everytime you call the method, it will have access to whatever object it's being called on. This can be really useful for conserving memory.

## Prototype Chains
1. Mechanism for making objects that resemble other objects.
1. JS provides the option of prototype chains - this makes one object behave as if it has all the properties of the other project by delegating the failed lookups from the first object to the second one.
1. [`extend`](https://api.jquery.com/jquery.extend/) can be used to copy one object to another, but this happens only one time.
1. Use `Object.create()` to create a new object based on an existing object. If a property can't be looked up upon in the new object, it will fall back to the existing one - ongoing lookup-time delegation.
```JavaScript
	var gold = {a:1};
	log(gold.a); // 1
	log(gold.z); // undefined

	var blue = extend({}, gold);
	blue.b = 2;
	log(blue.a); // 1
	log(blue.b); // 2
	log(blue.z); // undefined

	var rose = Object.create(gold);
	rose.b = 2;
	log(rose.a); // 1
	log(rose.b); // 2
	log(rose.z); // undefined

	gold.z = 3;
	log(blue.z); // undefined
	log(rose.z); // 3
```
1. There is a top level object that every JavaScript object eventually delegates to. This is where all the basic methods are provided for all objects. We call it the **object prototype** because it provides the shared properties of all objects in the entire system.
1. `.constructor` is used to tell what function was used to create a certain object. Like all properties, `.constructor` actually points to a different object that's stored elsewhere.
1. Some specical objects that you can make in JavaScript have extra features above and beyond the basic characteristics of all objects. Arrays, for example, have methods like `.indexOf()`and `.slice()`. Those array methods are stored in another prototype called the Array prototype. The array prototype in turn delegates to the Object prototype so that the non-unique parts of an array can be inherited from the Object prototype.

## Object Decorator Pattern
1. Code reuse is the practice of writing generalized code that can be relied upon to address a variety of similar goals - factoring.
1. Benefits: 
	1. Don't have to repeat yourself.
	1. Improve experience of code refractoring - easier!
1. It's common to use adjectives as the names for your decorator functions - [Udacity/Hackreactor Carlike Example](https://www.youtube.com/watch?v=XH-ZqDwQ7lk)
1. Its expensive (in terms of memory) to create function object which have the same properties everytime. Hence you probably want to think about delegation.


## Functional Classes
1. The difference between a decorator code and a class is that a class builds the object that it's going to augment, whereas a decorator accepts the object it's going to augment as an input.
```JavaScript
	// Decorator
	var amy = carlike({}, 1); // notice how object is included as an input

	var carlike = function(obj, loc) {
		obj.loc = loc;
		obj.move = function() {
			obj.loc++; // notice that how obj can be used to replace this since every object has it's own move method
			return obj;
		}
	}
``` 
```JavaScript
	// Class
	var amy = carlike(1);

	var Car = function(loc) {
		var obj = {loc: loc};
		obj.move = function() {
			obj.loc++;
		};
		return obj;
	};
	// Note: this basic form still result in duplicated methods, in this case .move.	
```
```JavaScript
	// Class - a better way
	var amy = carlike(1);

	var Car = function(loc) {
		var obj = {loc: loc};
		obj.move = move;
		return obj;
	};

	var move = function() {
		this.loc++;
	};

	// this arrangement may require you to edit both places (Car and the new function) everytime you want to add a new function. You could store all functions in a method object and pass it in.
```
1. A class is a construct that is capable of building a fleet of similar objects that all conform to roughly the same interface.
1. It's conventional to name your class with a capitalized noun. The functions that produce these fleets of similar objects are called constructor functions because their job is to construct objects that will qualify as members of the class.
1. The objects that get returned from these constructor function invocations, those are called instances, instances of the class.
```JavaScript
	// Class - store all methods in an object
	var amy = carlike(1);

	var Car = function(loc) {
		var obj = {loc: loc};
		extend(obj, Car.methods);
		return obj;
	};

	Car.methods = {
		move = function() {
			this.loc++;
		};
	};
```
1. In this case you can imagine that the function object that is reffered to by the variable Car might have a dot methods property pointing to the object that is used to stored all of Car methods.
1. It is important to note that there is no interaction between the properties of a function and what you expect to happen when you invoke that function. The invocation of the function only makes the lines inside the body execute. Invoking a function has no interaction with any of the properties of that function.

## Prototypical Classes
1. Instead of using `extend` to copy all the methods over, we can use the prototype object to store all the shared methods and make our instances delegate to that shared prototype object.
1. Build the object such that they delegate to a prototype object rather than copying all the methods using `extend`. We can use `Object.create(prototype)`. Copying methods is expensive.
1. The default object that comes with every function, is stored at the `key.prototype`.
1. So it becomes like this:
```JavaScript
	var Car = function(loc) {
		var obj = Object.create(Car.prototype); // notice how this part is repeated
		obj.loc = loc;
		return obj; // and this part is also repeated
	};

	Car.prototype.move = function() {
		this.loc++;
	}; // in previous example, a Car.methods object is created and have the Car class object delegated to it. But since the prototype object comes with every function, we don't need to create a new object like we did for Car.methods().
	var amy = Car(1);
	amy.move();
```
1. `key.prototype` is just a freely provided object to storing things with no additional special characteristics.
1. Every `key.prototype` object also has a `.constructor` property which points back to the function it came attached to. 
1. `amy instanceof Car` check to see if right operand's (Car) .prototype object can be found anywhere in the left operand's prototype chain (amy).

## Psuedoclassical Patterns
1. Refractoring the pure prototypical pattern to give the pseudo-classical pattern - it's called this way because it attempts to resemble the class systems from other languages by adding a thin layer of syntactic conveniences. 
1. JavaScript provides the keyword `new` - when `new` is used in front of a function invocation e.g. `var amy = new Car();`, the function is going to run in a special mode called Constructor Mode. Basically it will run the `Object.Create` and `return obj` part automatically as done in the prototypical classes example (think the `obj` is replaced by `this`).
1. In terms of the obj relationship, it is the same as prototypical patterns.
1. 
```JavaScript
	// this section to specify how each instance should be different from all the other instances - as with most programming languages
	var Car = function(loc) {
		this.loc = loc;
	};

	// this section to specify how all the instances of a class should be similar.
	Car.prototype.move = function() {
		this.loc++;
	};

	var amy = new Car(1);
```
1. Looking back at the functional version of this class (see the section), if without shared methods make no distinction between similarities and differences of each instance as all the code appears in one place.

## Superclass and Subclass
1. A superclass function is for creating a lot of similar objects and other classes will use the output from the superclass as their starting point. Think a superclass called `Vehicle` and `Sedan` and `Van` are it's subclasses.
```JavaScript
	// Functional Pattern
	var Car = function(loc) {
		var obj = {loc: loc}
		obj.move = function() {
			obj.loc++;
		};
		return obj;
	};

	var Van = function(loc) {
		var obj = Car(loc);
		obj.grab = function() {/*...*/};
		return obj;
	};

	var Cop = function(loc) {
		var obj = Car(loc);
		obj.call = function() {/*...*/};
		return obj;
	};
```

## Psuedoclassical Subclasses
1. Incorrect solutions when writing subclasses in pseudoclassical pattern:
```JavaScript
	var Car = function(loc) {
		this.loc = loc;
	};

	Car.prototype.move = function() {
		this.loc++;
	};

	var Van = function(loc) {
		// this.loc = loc; -- doing this is basically repeating the code

		// new Car(loc); -- problematic because this creates a new instance of Car in addition to a instance of Van, you already have an object to begin with when you do new Van().

		// this = new Car(loc); -- people use this way to assign the result of a new Car invocation to local keyword this. This is disallowed anyway and it doesn't solve the fact that you have two objects.

		// Car(loc); -- this doesn't work either. Since the keyword this in the Car function would refer to the global scope and Van is completely unaffected.

		Car.call(this, loc); // this is how we want to call it. The goal of this line is to invoke the Car function such that it's parameter this is bound to the Van instance and to do that we are also going to have to use the keyword this in the Van function - as it's bound to the instance.
	};

	var zed = new Car(3);
	zed.move();

	var amy = new Van(9);
	console.log(amy.loc) // this will work correctly now
	amy.move(); // this won't because the amy object doesn't delegate to Car.prototype, it's delegate to Van.prototype, which is delegate to Object.prototype
	amy.grab();	
```
1. Basically we want to run the `Car` function in the middle of the `Van` function but in such a way it modifies the new `Van` instance that's being created.
1. Use [`.call()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call) to run functions in whatever context we choose. In the example above, it basically means that the parameter this is going to behave even more similarly to a positional function parameter. 
1. Example below basically shows you how the number 3 is being passed to replace `this` in the function called.
```JavaScript
	var product = function(num,b) {
		return num * b;
	};

	var double = function(x) {
		return product(num, 2);
	};
	
	double(3);
```
```JavaScript
	var product =  function(b) {
		return this * b;
	};

	var double = function() {
		return product.call(this, 2);
	};
	
	double.call(3);
```
1. In the above example of Car we wired up the differentiation code. We also need to wire up the subclass prototype to the superclass prototype i.e. `Van.prototype` delegates to `Car.prototype`:
```JavaScript
	var Car = function(loc) {
		this.loc = loc;
	};

	Car.prototype.move = function() {
		this.loc++;
	};

	var Van = function(loc) {
		Car.call(this, loc);
	};

	// Van.prototype = Car.prototype -- JS doesn't do any copying when you assign one variable or property to be equal to another one. Think adding .grab() to Van will also add to Car

	Van.prototype = Object.create(Car.prototype) // this delegates Van.prototype to Car.prototype when fail to look up a certain method in Van.prototype.

	// Van.prototype = new Car(); -- this is problematic because we won't be able to pass the value loc when instantiating the car object hence it would throw an error. Due to long misunderstanding of JS, this technique has been widely accepted and standardized on for many years. You will see a lot of misguided documentation out there on reputable sites that endorses this broken pattern.

	Van.prototype.constructor = Van; // this is to add back the constructor function which was replaced when doing delegation

	Van.prototype.grab = function() {/*...*/};

	var zed = new Car(3);
	zed.move();

	var amy = new Van(9);
	amy.move(); // this works correctly now that Van.prototype correctly delegates to Car.prototype
	amy.grab();	
```
1. The psuedoclassical subclasses will work just fine after a reassignment like this: `Van.prototype = blah blah`  and that's because the instance delegation is set up only when the constructor actually runs. This prototype replacement happens right alongside the constructor definition and so that's plenty of time before we actually instantiate any objects.
1. Remember that the Van.prototype object was replaced. Hence we need to put back the constructor function back to Van.prototype. Otherwise, in this example, if run `console.log(amy.constructor)`, it will return `Car` since `Van.prototype` doesn't have a `.constructor` method and it delegates to `Car.prototype`, which has it's own `.constructor` method and returns `Car` as a result.