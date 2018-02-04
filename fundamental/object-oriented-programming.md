## Object Oriented Programming (OOP)

The advantage of Object Oriented Programming (OOP) is that it promotes code reusability. However, poorly designed code that leverages OOP often leads to [unwieldy and untestable code](https://medium.com/@xiaoyunyang/how-to-refactor-unwieldy-untestable-code-4a73d75cb80a) that's hard to extend and test.

There are two ways to do OOP:

1. Inheritance
2. Composition

Composition is superior. I will explain these two concepts using Salad. Suppose you have a business that uses a self-order Kiosk to lets people order salads. They can choose between different ingredients. How would you program your kisok to let your customers pick ingredients for their salads and put in an order?

**OOP with Inheritance**

In the classical inheritance paradigm, you make a Salad class, which includes lettuce, a meat of your choice and a salad dressing of your choice. 

```javascript
function Salad() {
    var meat = "no meat"
    var dressing = "no dressing"
    var greens = "lettuce"
    
    var meatChoices = ["chicken", "ham", "shrimp"]
    var dressingChoices = ["ranch", "thousand island", "caesar"]
    
    // Getters
    this.getMeatChoices = function() {
    	return meatChoices
    }
    this.getDressingChoices = function() {
    	return dressingChoices
    } 
    	
    // Setters
    this.setMeat = function(choice) {
        meat = meatChoices[choice]
    }
    this.setDressing = function(choice) {
        dressing = dressingChoices[choice]
    }
	this.getOrder = function() {
		return {greens: greens, meat: meat, dressing: dressing}
	}
}
```

Think of configuration as picking the ingredients for your salad. Some of these ingredients can be overwritten. Some (i.e., `greens`) cannot be overwritten.

```javascript
// Creates an instance of Salad
var mySalad = new Salad()

// Get default salad, which is only lettuce
mySalad.getOrder()
//> {greens: "lettuce", meat: "no meat", dressing: "no dressing"}

// Add meat
mySalad.getMeatChoices //> ["chicken", "ham", "shrimp"]
mySalad.setMeat(0)

// Add salad dressing
mySalad.getDressingChoices //> ["ranch", "thousand island", "caesar"]
mySalad.setDressing(2) //

// Get order
mySalad.getOrder()
//> {greens: "lettuce", meat: "chicken", dressing: "caesar"}
```

With this approach, you can create many different kinds of salads varying in meat and salad dressing. However, what you want to expand your salad options to include other kinds of greens like kale or spinach?

The Salad object you have written comes with lettuce by default, which you **cannot override** with other greens like kale or spinach. You have to refactor your code now to make `greens` also configurable by adding a new setter function called `this.setGreens(myGreens)`, which should look a lot like the other setter functions you already have.

That's an easy enough refactor. It requires your kiosks to be updated to use the new software but it's not impacting any existing functionality (other functions don't have to change).

```javascript
function Salad() {
 // ...
 
 var greensChoices = ["lettuce", "kale", "spinach"] // <== Add this
 
 this.getGreensChoices = function() { // <== Add this 
 	return greensChoices
 }
 this.setGreens = function(choice) { // <== Add this
 	greens = greensChoices[choice]
 }

 // ...
}
```

Now you want to add another ingredient to the salad: `cheese`. Your customers get only one choice of cheese, like feta, swiss, or parmesan. Adding the new ingredient is going to require more effort than making an ingredient that wasn't able to be overwritten overwritable. Not only do you have to create a setter for the cheese, you have to change your `getOrder` function to include `cheese` as part of the order.

```javascript
function Salad() {
	var cheese = "no cheese" // <== Add this
	var cheeseChoices = ["feta", "swiss", "parmesan"] // <== Add this
	
	// ...
	
 	this.getCheeseChoices = function() { // <== Add this
 		return cheeseChoices
 	}
 	this.setCheese = function(choice) { // <== Add this
 		greens = cheeseChoices(choice)
 	}

 	// ...
 	
 	this.getOrder = function() { // <== Change this
		return {greens: greens, meat: meat, dressing: dressing, cheese: cheese}
	}
}
```

As your salad business grows, your customer demands more choices and customizability to their salads, you keep adding things to and changing things in `Salad`. You also want to make special ingredients only available on certain day of the week or holidays so you are adding `Date` to your code so you can display 

and offer your customers the option to skip all the customization and go straight to menu items like greek salad or caesar salad. Before you know it, your `Salad` code starts to becomes riddled with conditionals, non-deterministic behavior tightly coupled with the environment in which it runs.  It's difficult to unit test because you have to simulate the environment that produces certain results. For example, you decided to add `Date` logic to your `getMeatChoices` function so it will only display "shrimp" on Tuesdays. This means your `Salad` function will literally give you different results when you run it at different times. This is the definition of non-determinism. Further, you can't immediately tell if your added functionality is going to break existing functionality without acceptance testing (i.e., load the new code into a kiosk and click on buttons to see what happens). In the example, you have to wait until Tuesday to see if your `getMeatChoices` returns "shrimp" as an option if you don't have a test suite that can simulate time, or other environmental variables.

There's stuff you can do like refactoring your code everytime you add functionality to it that you think will make it become untestable. But refactoring takes time and effort. You are running a busy salad shop by yourself so you don't have the time to do it yourself and you don't make enough money to hire a developer after paying your kitchen staff, rent, and taxes. This is not a good situation to be in.

Let's rewind and see how we can design our `Salad` code differently so we won't run into these problems.

**OOP with Composition**
