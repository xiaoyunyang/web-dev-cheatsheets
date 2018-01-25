## The JavaScript Closure
### What is Closure?

[One definition](https://medium.freecodecamp.org/lets-learn-javascript-closures-66feb44f6a44) is this:
> Closures are functions that refer to independent (free) variables. In other words, the function defined in the closure ‘remembers’ the environment in which it was created.

I like this definition from [Secrets of the JavaScript Ninja](https://www.manning.com/books/secrets-of-the-javascript-ninja-second-edition) better:
>A closure is a way to access and manipulate external variables from within a function.


### Closure in action:

Closure can let you do some cool things, like object oriented programming in JavaScript:

```javascript
function Person(name) {
    
var secret = "secret!"
this.name = name

this.setName = function(newName) { this.name = newName}
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
a.name //> Foo
    
a.secret //> undefined
a.getSecret() //> Secret!
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

Let's try a couple of things based on the function we created from the previous example:

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
* `incrementUntil` also maintains a closured reference to `Num` and can read and modify it. The scope of `Num` is `window` if you typed this example as-is directly into the browser console.
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
	

The "fix" fo `myFun2` seems to work fine but it lends itself to a lot of duplicate code. What if we have to increment variables inside of other functions? It's not good practice to copy-paste the same function into multiple places. We can fix that with **partial application**:
	
```javascript
var incrementUntil = function(max) {
	var inc = function(num) { 
		if(num >= max) return num 
		return inc(num+1)
	}
	return (num) => inc(num)
}
	
incrementUntil(4)(1) //> 4	
```  

Partial application is basically a design pattern where your function returns another function that takes an argument.so you can call your closure like this: `myFun(arg1)(arg2)`, which is equivalent to

```
const myFun2 = myFun(arg1)myFun2(arg2)```

It’s called currying and partial application - a key concept of functional programming, a key concept. Closure is a powerful design pattern because you can continuing chaining things like `myFun(arg1)(arg2)(arg3)…`. `myFun` does some calculation with using `arg1`, returns another function that takes the result of `myFun`'s calculation on `arg1` and `arg2` as input, then processes things further by calling a third function, which in turn calls a fourth function and so on. You can think of it like an assembly line in a factory.


```javascript
// A practical example of closure

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
    if (param == "username") return getUsernameFromURL(pathname)
    else if (param == "pathname") return getPathnameFromURL(pathname)
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


### Resources

* [Everything You Need to Know about Scopes](https://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/) - which discusses closure.
* [Secrets of the JavaScript Ninja](https://www.manning.com/books/secrets-of-the-javascript-ninja-second-edition)