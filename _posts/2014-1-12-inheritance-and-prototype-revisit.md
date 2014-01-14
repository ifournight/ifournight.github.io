---
layout: post
title: Revisit inheritance and prototype of JS
date: 2014-1-13 11:36
---
##Revisit Object

> An object is a container of properties, ... A property name can be any string, include the empty string. A property value can be any JavaScipt value except for `undefined`.

Of course property name should be string, but when in practive this week I just write a object which has a number property. This should be a syntax error, but the error never happen, my guess is that the JS transcriptor just implicitly tranfer the number to the corresponding string.

Get the value of `undefined` will cause `TypeError`, so there are serveral tricks to avoid this, leared from JavaScript: The Good Parts：

Use || operator to assign default value:

{% highlight JavaScript %}
var value = object["property"] || "defaultValue";
{% endhighlight %}

Using && operator to protect getting value from `undefined`:

{% highlight JavaScript %}
if (object.someProperty && object.someProperty.someValue) {};
{% endhighlight %}

###delete

delete will only remove the object's own property, it will not touch the prototype chain.

##Revisit prototype

###Some wireds.
- constructor must be a `Function`.
- In classical inheritance, only constructor has a prototype property pointing at its prototype, not the instance of the constructor.In ECMA-262 fifth edition，instance of the constructor will have a implict potiner[[Prototype]], mordern browsers implement the [[Prototype]] as `__super__`.

##Revisit inheritance

> What matters about an object is what it can do, not what it is descended from.

I highly agree with what DC said in *The Good Part book*, and I try to understand 4 inheritance patterns he metioned in this book. There's maybe much more complicated patterns, but:

> Much more complicated constructions are possible, but it is usually best to keep it simple.

###Pseudoclassical

It's important to understand what `new` really do if it is a function:

{% highlight JavaScript %}
if (typeof Object.beget != 'function') {
	Object.create = function(p) {
		// Create a plain constructor.
		var F = new Function() {};
		// Assign prototype.
		F.prototype = p;
		// Well, I guess we can't use new to explain new,
		// so I am still confused about this line.
		return new F();
	}
}

Function.method('new', function() {
	// Create a new object that inherits from the constructor's prototype.
	// That's is why the instance don't have prototype property.
	var that = Object.create(this.prototype);

	// Invoke the constructor, binding -this- to 
	// the new object.
	var other = this.apply(that, arguments);

	// If its return value isn't an object,
	// substitue the new object.
	return (typeof other === 'object' && other) || that;
});
{% endhighlight %}

There's still one line code I can't understand, which is DC use `new` prefix in beget method to explain new method. 
Here is the internal process that in my understanding:

1. The constructor as a function, when used with `new` prefix, create a plain object, and assign the object's [[prototype]] to constructor's prototype.
2. The code inside constructor function execute under the context of the new object, like a process of initilization.
3. If the constructor function has a return value, the value is returned, instead the new object is returned.

**NOTE**: If using pseudoclassical, Capitalize and only capitalize the Contructor, always use `new` prefix.

###Prototypal
Prototypal pattern still use prototype, what it get rid of is 'The entire class stuff' and the `new` prefix. The example from *The Good Part*:

{% highlight JavaScript %}
var myCat = Object.create(myMammal);
{% endhighlight %}

There's no class in prototypal pattern, methods and propertys of one object can be fetched by another object directly.
So there's no constructor, no instance, there's only objects which inherit from its prototype.

###Functional

Functional gives up the prototype, and use function to achieve inheritance. Four steps of **Functional** inheritance:

1. Create a new object, with
	- Object literal
	- `new` Constructor(Pseudoclassical)
	- Object.create(Prototypal)
2. (Optionally) defines private instances variables and methods.
3. Add methods into the new object, which can access the private instances variables and methods.
4. return the new object.

The pseudocode template: 

{% highlight JavaScript %}
var constructor = function(spec, my) {
	var that // other private instance variables;
	my = my | {};

	// Add shared variables and functions to my

	that = a new object;

	// Add privileged methods to that

	return that;
}
{% endhighlight %}

**Durable Object**:
> If we create an object in the functional style, and if all of the methods of the object make no use of `this` or `that`, then the object is durable. A durable object is simply a collection of functions that act as capabilities.

