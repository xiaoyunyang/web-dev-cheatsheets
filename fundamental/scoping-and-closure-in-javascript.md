## The JavaScript Closure
### What is Closure?

[One definition](https://medium.freecodecamp.org/lets-learn-javascript-closures-66feb44f6a44) is this:
> Closures are functions that refer to independent (free) variables. In other words, the function defined in the closure ‘remembers’ the environment in which it was created.

[Another definition](https://medium.freecodecamp.org/3-questions-to-watch-out-for-in-a-javascript-interview-725012834ccb)
> A closure is basically when an inner function has access to variables outside of its scope. Closures can be used for things like [implementing privacy](https://medium.com/written-in-code/practical-uses-for-closures-c65640ae7304) and creating [function factories](https://medium.com/javascript-scene/javascript-factory-functions-vs-constructor-functions-vs-classes-2f22ceddf33e#.1817w0lmb).

I like this definition from [Secrets of the JavaScript Ninja](https://www.manning.com/books/secrets-of-the-javascript-ninja-second-edition) better:
>A closure is a way to access and manipulate external variables from within a function.



### Closure in action:

Closure can let you do some cool things, like object oriented programming in JavaScript:

```javascript
function Person(name) {
    
var secret = "secret!"
this.name = name

this.setName = function(newName) { this.name = newName }
this.setNameToFoo = function() { this.name = foo }
this.getSecret = function() { return secret }
}
    
var a = new Person("Max")
    
a.name //> "Max"
a.setName("Oliver")
a.name //> "Oliver"
a.setNameToFoo() //> ERROR: foo is undefined
    
var foo = "Foo"
a.setNameToFoo()
a.name //> "Foo"
    
a.secret //> undefined
a.getSecret() //> "Secret!"
```
	
And recursion:


```javascript
var incrementUntil = function(max) { 
  if(num >= max) 
    return num 
  
  num++
  incrementUntil(max)
}
	
var num = 0
incrementUntil(3)
num //> 3
	
```

Let's try a couple of things based on the function we created in the recursion example:

```javascript
num = 0
var myFun = function() { 
	incrementUntil(3)
	return num 
}
myFun() //> 3
num //> 3
```

So far so good...

```javascript
num = 0
var myFun2 = function() { 
	var num = -1
	incrementUntil(3)
	return num 
}
myFun2() //> -1 .......Why?
num //> 3

```

...Uh oh what happened?

	
**Some Observations:**
	
* `incrementUntil` can call itself because it maintains a closured reference to itself. This makes recursion possible in JavaScript.
* `incrementUntil` also maintains a closured reference to `num` and can read and modify it. The scope of `num` is `window` if you typed this example as-is directly into the browser console.
* Although `incrementUntil` is being executed inside of `myFun2`, it didn't modify the `num` in `myFun2`'s scope because `incrementUntil` only recognizes the `num` that was inside of its parent's scope when it was first created (e.g., `window`). It doesn't recognize the `num` inside of the caller, `myFun2`, because it doesn't maintain a closured reference to its caller's scope.


Here's how we can fix `myFun2` so it can leverage the `incrementUntil` function to modify the `num` inside of its own scope.
	
```javascript
var myFun2 = function() { 
	var num = -1
	
	function incrementUntil(max) { 
		if(num >= max) 
		return num 
		num++
		incrementUntil(max)
	}
	incrementUntil(3)
	
	return num 
}
	
myFun2() //> 3
```
	

The "fix" for `myFun2` seems to work fine but it lends itself to a lot of duplicate code. What if we have to increment variables inside of other functions? It's not good practice to copy-paste the same function/code into multiple places. Let's refactor our `incrementUntil` function using **partial application**:
	
```javascript
var incrementUntil = function(max) {
	var inc = function(num) { 
		if(num >= max) return num 
		return inc(num+1)
	}
	return (num) => inc(num)
}
	
incrementUntil(4)(1) //> 4
incrementUntil(8)(3) //> 8

var num = 1
incrementUntil(8)(num) //> 8
num //> 1 .... Good! Our function is pure!
```  

The refactored `incrementUntil` function does not read or modify anything outside of its own scope so it's a pure function, which illustrates a core principle of functional programming. You call the function as follows: `incrementUntil(max)(num)`, which reads "increment until `max` starting from `num`".

A really nice thing about partial application is that we can partially evaluate the final solution by providing our function one of the arguments it needs. Then we can save the intermediate solution to a variable so we can save ourselves some processing time by eliminating the need to evaluate the same intermediate solution every time we call the function to do similar things. Here's what I mean by that in code:

```javascript
var multThenAdd = mul => {
	return add => num => {
		return num * mul + add
	}
}

var timesTwoPlusFour = multThenAdd(2)(4)

timesTwoPlusFour(1) //> 6
timesTwoPlusFour(10) //> 24
```

Now you've seen partial application in action, here's a high level discussion on the concept of partial application (also called currying by some).

Partial application is basically a design pattern where your function returns another function that takes an argument.so you can call your function like this: `myFun(arg1)(arg2)`, which is equivalent to

```
const myFun2 = myFun(arg1)myFun2(arg2)```

Partial application is a powerful design pattern because you can continuing chaining things like `myFun(arg1)(arg2)(arg3)…` myFun partially applies `arg1`, returns another function that takes `arg2` as argument and applies `arg2` to the result of `myFun` evaluated over `arg1`. So on and so forth.

Partial application is a powerful design pattern because you can continuing chaining things like `myFun(arg1)(arg2)(arg3)…`. What’s happening here is akin to an assembly line in a factory: One argument at a time is applied to myFun to evaluate a better and better partial solution until all the arguments are applied to provide a complete solution.

Here's an actual piece of code I wrote for a Node/Express app:

```javascript
const getInfoFromURL = path => {
  const URL = require("url").URL;
  const myUrl = new URL(path)
  const pathname = myUrl.pathname

  const getUsernameFromURL = pathname => {
    const regex = new RegExp('/@');
    const username = pathname.split(regex).slice(1)[0]
    if(!username) {
      return "Error in parsing: URL needs to be in format://hostname:port/@username"
    }
    return username
  }
  const getPathnameFromURL = pathname => {
    const regex = new RegExp('/');
    const name = pathname.split(regex).slice(1)[0]
    if(!name) {
      return "Error in parsing: URL needs to be in format://hostname:port/pathname"
    }
    return name
  }

  return (param) => {
    if (param === "username") return getUsernameFromURL(pathname)
    else if (param === "pathname") return getPathnameFromURL(pathname)
    else return "error"
  }
}

module.exports = getInfoFromURL

```
How you call the above code?

```javascript
// You should get "xiaoyunyang" 
getInfoFromURL("https://medium.com/@xiaoyunyang")("username")
// You should get "@xiaoyunyang
getInfoFromURL(path)("pathname")
```


## Closure Under The Hood

For background, read [the primer on execution context in JavaScript](http://davidshariff.com/blog/what-is-the-execution-context-in-javascript/)

[This article](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8) explains it well:

>One intricacy of JavaScript is how it looks for variables. If it can’t find a variable in its local execution context, it will look for it in its calling context. And if not found there in its calling context.


> When we start the program, we start in the global execution context. Some variables are declared within the global execution context. We call these global variables. When the program calls a function, what happens? A few steps:

> 1. JavaScript creates a new execution context, a local execution context
2. That local execution context will have its own set of variables, these variables will be local to that execution context.
2. The new execution context is thrown onto the execution stack. Think of the execution stack as a mechanism to keep track of where the program is in its execution

If you came from a C or embedded systems background, this should be very familiar. The "global execution context" is like the main operating loop, which contains many subroutines (i.e., helper functions. The "local execution context" is like the stack frame that's created whenever the subroutine starts executing; however, instead of pushing the stack frame onto the [call stack](https://www.wikiwand.com/en/Call_stack), JavaScript pushes it into the heap.  Storing local execution context of a function on the call stack makes the context lost after the function finishes executing. Storing the local execution context in the heap allows your local execution context (i.e., function) to stay alive after it finishes executing. All you need to access that local execution context is a reference to the subroutine.

Languages which support closure (such as JavaScript, Swift and Ruby) will allow you to keep a reference to a scope (including its parent scopes), even after the block in which those variables were declared has finished executing, provided you keep a reference to that block or function somewhere.

Another way of thinking about this is a closure is a persistent scope which holds on to local variables even after the code execution has moved out of that block. The scope object, and all its local variables, are tied to the function, and will persist as long as that function persists.

Closure captures any variables that were in scope when the function was first defined and make these variables available when we later call the function, even if we call the function in a completely different context. Note, the closure captures not the value of the variable nor the value of the reference the variable points to, but the variable itself!


**Resources**
* [I Never Understood JavaScript Closures](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8) 😍
* [Everything You Need to Know about Scopes](https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/) - which discusses closure.
* [Secrets of the JavaScript Ninja](https://www.manning.com/books/secrets-of-the-javascript-ninja-second-edition)
* [Making Functional Programming Click](https://hackernoon.com/making-functional-programming-click-836d4715baf2)
* [MDN Closure Tutorial](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)
* [Tricky Closure Inteview Question](https://medium.com/coderbyte/a-tricky-javascript-interview-question-asked-by-google-and-amazon-48d212890703)
* [JavaScript Closure Questions](https://coderbyte.com/algorithm/3-common-javascript-closure-questions)
* [Three Questions to watch out for in JavaScript Interview](https://medium.freecodecamp.org/3-questions-to-watch-out-for-in-a-javascript-interview-725012834ccb)
* [JS Scope](https://www.w3schools.com/js/js_scope.asp)
* [Scotch.io's scope, closure, and context tutorial](https://scotch.io/tutorials/understanding-scope-in-javascript) 😍

### Tricky Questions on Closure and Scoping in JS

1. What will this output?
	
	```javascript
	for (var i = 0; i < 3; i++) {
	  setTimeout(function() { console.log(i); }, 1000 + i);
	}
	```
	[Solution](https://coderbyte.com/algorithm/3-common-javascript-closure-questions)

2. What will this output?

	```javascript
	const arr = [10, 12, 15, 21];
	for (var i = 0; i < arr.length; i++) {
	  setTimeout(function() {
	    console.log('Index: ' + i + ', element: ' + arr[i]);
	  }, 3000);
	}
	```
	[Solution](https://medium.com/coderbyte/a-tricky-javascript-interview-question-asked-by-google-and-amazon-48d212890703)

3. Write a function that would allow you to do this.

	```javascript
	var addSix = createBase(6);
	addSix(10); // returns 16
	addSix(21); // returns 27
	```

	Solution:

	```javascript
	var createBase = (base) => (num) => base + num
	var addSix = createBase(6)
	addSix(10) //> 16
	addSix(21) //> 21
	```
	
### Gotchas
**Scope vs. context**

While scope refers to the visibility of variables from different parts of your code, context refers to the use of `this` keyword in your code.

What is `this`?

```javascript
var fun = function() {
	var name = "Max"
	return this 
}
fun() //> Window { postMessage: f, blur ..... }
fun().name //> undefined
```

Why was `fun().name` undefined? Well because it's  `var` and is private to the function `fun`. Being private means you can't access it from the outside.

You can access `name` if you made this change:

```javascript
var fun = function() {
	this.name = "Max"
	return this
}
fun().name //> "Max"
```

Why does the above code work? When we are assigning a value to `this.name`, we are creating a variable called `name` inside the context of the function `fun`.

Or if you made this change:

```javascript
var fun = function() {
	var name = "Max"
	return {this: this, name: name}
}
fun().this //> Window { postMessage: f, blur ..... }
fun().name //> "Max"
```

When we called `fun()` in the code above, the context for `fun` was returned. This is what `fun`'s `this` gives you:
	
```
Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, frames: Window, …}
```
	
But the context of `fun` is the context of `Window`! This can be proven with a simple test:

```javascript
fun().this === this //> true
```
 
Here's where it gets confusing with ES6. ES6 let's you create an arrow function, which works like a function but is not a `function`. So before you start changing all your `function` to `=>`, beware that doing this could break your code. 
mbeaudru provided [this great summary](https://github.com/mbeaudru/modern-js-cheatsheet#this-reference);
>An arrow function doesn't create a new `this`, it grabs it from its surrounding instead. Let's refactor the code above to create an arrow function and see what happens:


```javascript
var fun = function() {
	var name = "foo"
	var innerFun = function() {
	var name = "bar"
		return {this: this, name: this.name}
	}
	return innerFun()
}

fun() //> {this: Window, name: "bar"}
fun().this === this
```
The `this` of the `innerFun` is still `Window`. That hasn't changed. 

```javascript
var fun = function() {
	this.name = "foo"
	var innerFun = function() {
		var name = "bar"
		return {fun: "inner", this: this, name: name}
	}
return [innerFun(), {fun: "outer", this: this, name: this.name}]
} 

fun()[0] //> {fun: "inner", this: Window, name: "bar"}
fun()[1] //> {fun: "outer", this: Window, name: "foo"}
```

If you removed the name `var` in front of the `name` in the inner function, you'll get different answers

```javascript
var fun = function() {
	this.name = "foo"
	var innerFun = function() {
		name = "bar"
		return {fun: "inner", this: this, name: name}
	}
return [innerFun(), {fun: "outer", this: this, name: this.name}]
} 

fun()[0] //> {fun: "inner", this: Window, name: "bar"}
fun()[1] //> {fun: "outer", this: Window, name: "bar"}
```

## JavaScript and Context

[Why and how to bind methods](http://reactkungfu.com/2015/07/why-and-how-to-bind-methods-in-your-react-component-classes/)
>In languages like Ruby or Java, this (or in case of Ruby self) will always point to the object in which your method is defined. So in Ruby if you are working on the foo method inside the Bar class, self will always point to the object which is the instance of the Bar class.

>JavaScript works quite surprisingly here. Because in JavaScript function context is defined while calling the function, not while defining it! This is what can surprise many when coming to JS from different fields. Such late binding is a powerful mechanism which allows us to re-use loosely coupled functions in variety of contexts.


### Bind

> Bounded function in JavaScript is a function that is bounded to a given context. That means no matter how you call it, the context of the call will stay the same. The only exception is the new operator which always return a new context.


```javascript
function add(x, y) {
  this.result += x + y;
  return this.result;
}

var computation1 = { result: 0 };
var boundedAdd = add.bind(computation1);

boundedAdd(-1,-2) //> -3
boundedAdd(-1,-2) //> -6

// Change context
computation1 = { result: 12 };
boundedAdd = add.bind(computation1);
boundedAdd(-1,-2) //> 9
```

### Class

> ECMAScript 2015 (ECMAScript 6) introduced a new class syntax that can be used to create React component classes. In fact, this class syntax is a syntactic sugar for the old, prototype system of object-oriented JavaScript.

```javascript
class Foo {
  constructor() {
    this.x = 2;
    this.y = 4;
  }

  bar() {
    // ...
  }

  baz() {
    // ...
  }
}
```

Is roughly the same as:

```javascript
function Foo() {
  this.x = 2;
  this.y = 4;

  this.bar = function() { // ... };
  this.baz = function() { // ... };
}
```