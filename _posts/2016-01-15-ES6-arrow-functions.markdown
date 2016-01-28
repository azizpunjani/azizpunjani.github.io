---
layout: post
title:  "ES6: arrow functions"
date:   2016-01-15 20:23:40
categories: javascript ecmascript6
---

ES6 provides a new way to create functions using the `=>` operator. These functions are
called arrow functions. Arrow functions have a shorter, more concise syntax.
<!--more-->

Let's take a look at an example:

{%highlight javascript %}
const add = ( a, b ) => a + b;

console.log( add( 2, 3 ) ); // 5
{% endhighlight %}

The ES5 version of this code would look something like this.

{%highlight javascript %}
var add = function ( a, b ) {
  return a + b;
}

console.log( add( 2, 3 ) ); // 5
{% endhighlight %}

Notice in the first part of declaring an arrow function, the `function` keyword has been dropped i.e `( a, b )`. If there was only
one parameter to the function the parenthesis are optional, here's an example:

{%highlight javascript %}
const numbers = [ 1, 5, 8, 10, 15 ];

const greaterThanEight = numbers.filter( number => number > 8 );

console.log( greaterThanEight ); // [ 10, 15 ]
{% endhighlight %}

As you can see in the example above, we pass an arrow function to `Array.prototype.filter()` and since there's only one parameter
`number`, we can leave off the parenthesis. We could have left the parenthesis on, for example `( number ) => number > 8` achieving
the same result.

The second part of arrow functions is the return. In the code above since we have just one expression `number + 8`, that expression is evaluated and the result is returned implicitly. It's equivalent to writing

{%highlight javascript %}
function ( number ) {
  return number > 8;
}
{% endhighlight %}

If there needs to be multiple expressions, then `{ }` needs to be used to wrap the expressions. Here's a contrived example:


{%highlight javascript %}
const users = [
  { id: 1, firstName: 'Mary', lastName: 'Swanson' },
  { id: 2, firstName: 'Henry', lastName: 'Jackson' }
];

const deleteUser = ( id ) => {
  const index = users.findIndex( user => user.id === id );
  users.splice( index, 1 );
}

// deleteUser with id 1
deleteUser( 1 );
{% endhighlight %}

The `deleteUser()` function has multiple expressions and hence is wrapped with curly braces. The arrow function passed to
the [Array.prototype.findIndex()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex) method only contains one expression and is not wrapped with curly braces.

### The "this" value within an arrow function
The value of the `this` keyword within an arrow function is the value of `this` from the enclosing scope, which could
be the enclosing function scope or global scope. Let's take a look at an example:

{%highlight javascript %}
const person = {
  name: 'Aziz Punjani',

  logName: function (){
    console.log( this.name );
  }
};

person.logName(); // Aziz Punjani
{% endhighlight %}

The code above works as we would expect. But what if we wrap `console.log( this.name )` with a `setTimeout()`?

{%highlight javascript %}
const person = {
  name: 'Aziz Punjani',

  logName: function () {
    setTimeout(function () {
      console.log( this.name );
    }, 1000);
  }
};

person.logName();
{% endhighlight %}

In the code above, nothing is logged, why? Because the anonymous function creates a new scope and "this" within the anonymous
function is not the same "this" as the enclosing function. Arrow functions to the rescue! Let's see how we can get the code working
using an arrow function instead of a regular function.

{%highlight javascript %}
const person = {
  name: 'Aziz Punjani',

  logName: function () {
    setTimeout(() => {
      console.log( this.name );
    }, 1000);
  }
};

person.logName();
{% endhighlight %}

The code above works, because, as mentioned before, the arrow function's "this" value is the "this" value from the enclosing function i.e from its [lexical scope](https://en.wikipedia.org/wiki/Scope_(computer_science)#Lexical_scoping).

Another thing to be aware about is that arrow functions also reference [arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) of the enclosing function. In the example below, the arguments of the enclosing `log()` function are logged to the console, and not the `arguments` passed to `Array.prototype.map()`.

{%highlight javascript %}
function log( names ) {
  names.map(() => {
    console.log( arguments );
  });
}

log( [ 'Barney', 'Wilma', 'Fred' ] );
{% endhighlight %}
