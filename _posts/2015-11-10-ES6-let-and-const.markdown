---
layout: post
title:  "ES6: let and const"
date:   2015-11-10 18:52:57
categories: javascript ecmascript6
---

ES6 introduces the new `let` and `const` keywords. Up until ES5, Javascript only had function scope and global scope. The `let` keyword makes it possible to declare block scoped variables.

This blog post explores function scope, block scope and how it relates to the `let` and `const` keywords. It also briefly discusses
something known as the Temporal Dead Zone (TDZ).

Before we get to block scope let's talk a little about function scope.

<!--more-->

### Function Scope
Variables declared within a function using the `var` keyword, that are only accessible within the function, are said to have function scope.

{%highlight javascript %}

function outer() {
  var firstName = 'Aziz';

  function inner() {
      var lastName = 'Punjani';

      if ( true ) {
          var middleInitial = 'S';
      }

      console.log( firstName );
      console.log( middleInitial );
  }

  inner();
  console.log( lastName );
}

outer();

// Output

// Aziz
// S
// ReferenceError: lastName is not defined

{% endhighlight %}

In the example above, `lastName` is scoped to the inner function and is not accessible outside that function. `firstName` however is accessible
throughout the function.

The reason that `firstName` is accessible within the inner function has more to do with [lexical scope](https://en.wikipedia.org/wiki/Scope_(computer_science)#Lexical_scoping) which is outside the scope of this blog post (pun intended).

Notice how the variable `middleInitial` is accessible outside of the if block within the inner function? We will soon learn how to limit the scope of `middleInitial` to the if block.

### Block
What constitutes a block? A block is any code within curly braces that's <em>not</em> an object. Note that loop variables are considered to be part of the loop block. Below are examples of blocks.

{% highlight javascript %}

// Some examples of blocks

{
    ...
}


if ( true ) {
    ...
}


while( true ) {
    ...
}

function anotherBlock() {
    ...
}

for( let i = 0; i < 10; i++ ) {
    ...
}

{% endhighlight %}

### let
When the scope of a variable declared within a block is restricted to that block, it is said to have block scope.

The example below shows a variable that is only accessible within the block that it was declared in.

{% highlight javascript %}

{
    let name = 'block scope';
    console.log( name ); // block scope
}

console.log( name ); // ReferenceError

{% endhighlight %}

Attempting to access `name` outside of the block results in a ReferenceError.

Let's attempt the same code but replace `let` with `var`.

{% highlight javascript %}

{
    var name = 'global scope';
    console.log( name ); // global scope
}

console.log( name ); // global scope

{% endhighlight %}

In the above example, the `name` variable within the block is declared in the global scope, hence, is accessible outside of the block.

Variables declared with `let` outside of blocks behave in the same manner that variables declared with `var` outside of functions do, that is, they
are accessible globally.

{% highlight javascript %}

let name = 'I am global';

{
    let name = 'I am block scoped';
    console.log( name ); // I am block scoped
}

console.log( name ); // I am global

{% endhighlight %}

Since the first `name` variable was declared outside of a block, it is accessible globally. The `name` variable declared within the block
does not overwrite the global variable because the `name` variable within the block has block scope.

### const
The `const` keyword behaves similar to `let` in terms of being block scoped, but is different because
it's used to create a variable that, once assigned, cannot be reassigned. In the future, JS engines could potentially take advantage of this fact.

Let's take a look at a few examples.

{%highlight javascript%}

const name = 'Aziz';
name = 'Bob'; // throws read-only exception

{%endhighlight %}

Reassigning the `name` variable in the code above throws a read-only exception.

It's important to note that variables declared with `const` *are* mutable
and this is sometimes a source of confusion. The example below demonstrates
that an object and array are mutable even though they are defined using the `const` keyword.

{%highlight javascript%}

const hobbies = [ 'photography' ];
const person = {
    firstName: 'Aziz'
};

person.lastName = 'Punjani';

hobbies.push( 'playing musical instruments' );

{%endhighlight %}

The only reason you would use a `const` keyword in the code above is if you didn't want the `hobbies` and `person` variables
to get reassigned. The code below would throw a read-only exception as you would expect.

{%highlight javascript%}

const hobbies = [ 'photography' ];
const person = {
    firstName: 'Aziz'
};

hobbies = []; // throws read-only exception
person = {}; // throws read-only exception

{%endhighlight %}


### Temporal Dead Zone (TDZ)
Simply put, TDZ is the point in code where the variable is in scope but uninitialized. Accessing variable declared with `let` and `const` in the TDZ will result in a ReferenceError.

{%highlight javascript%}

{
    console.log( name ); // ReferenceError
    let name = 'aziz';
}

{%endhighlight %}

Let's take a look at what happens when we use the `var` keyword for similar code above.

{%highlight javascript%}

{
    console.log( name ); // undefined
    var name = 'aziz';
}

{%endhighlight %}

As you see, the code above does *not* throw a ReferenceError, this is expected behavior as `name` is [hoisted](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html) and initialized to undefined. Variables
declared using `let` and `const` are hoisted as well, but cannot be accessed until the `let` or `const` statement is evaulated.

### Migrating from `var` to `let` and `const`
We recently updated our open source repositories to use the `let` and `const` keywords.
Our approach was simple, replace `var` with `const` unless the variable is reassigned at a later point in the code, in which case, the variable should be declared using `let`.

There's [varying](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20&%20beyond/ch2.md#const-or-not) [opinions](https://davidwalsh.name/for-and-against-let),
on this subject and some people believe that variables declared using `var` still have their place as function scoped variables. After some
discussion and experimentation, we did not see a need to use `var` within our code base. We are [not alone](https://github.com/airbnb/javascript#2.1) in our decision.

The other change we made was assigning variables [closer to where they are being used](https://github.com/airbnb/javascript#13.4). Initially when using the `var` keyword we would declare our variables at the top of the function body, this mitigated hoisting bugs, but now that we use `let` and `const` which do not suffer from the same issues as the `var` keyword there's not a benefit to doing that.
