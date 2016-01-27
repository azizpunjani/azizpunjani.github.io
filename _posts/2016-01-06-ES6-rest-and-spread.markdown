---
layout: post
title:  "ES6: spread operator and rest parameters"
date:   2016-01-06 19:20:30
categories: javascript ecmascript6
---

The ES6 spread operator and rest parameter is represented by the `...` token. The usage of this
token will determine if it's a spread or rest operation.

<!--more-->

### Spread operator
A spread operator used in front of an [iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) such as an array, splits the values into individual values. Here's an example:

{%highlight javascript %}
console.log( ...[ 'first', 'second', 'third' ] ); // first second third
{% endhighlight %}

A common use case for the spread operator is to spread the values of an array and pass them in as arguments to a function.
Let's look at an example:

{%highlight javascript %}
function add( a, b ) {
    return a + b;
}

const numbers = [ [ 2, 3 ], [ 4, 5 ] ];

// Call add() for each one of the arrays within the numbers array
const results = numbers.map(
  numbersArray => add( ...numbersArray )
);

console.log( results ); // [ 5, 9 ]
{% endhighlight %}

The ES5 version for the code above would look something like this.
{%highlight javascript %}
function add( a, b ) {
    return a + b;
}

const numbers = [ [ 2, 3 ], [ 4, 5 ] ];

// Call add() for each one of the arrays within the numbers array
const results = numbers.map(
  function ( numbersArray ) {
    add.apply( null, numbersArray )
  }
);

console.log( results ); // [ 5, 9 ]
{% endhighlight %}

As you can see, the code uses the [apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) method which
calls the add method with a `null` "this" value and arguments provided as an array.

### Other usages of the spread operator
The spread operator can also be used to expand a value in other contexts such as, within another array. Let's take
a look at an example.

{%highlight javascript %}
const first = [ 1, 2 ];
const second = [ 3, 4 ];
const third = [ 5, 6 ];

const all = [ ...first, ...second, ...third ];

console.log( all ); // 1, 2, 3, 4, 5, 6
{% endhighlight %}

### Rest parameters
When the `...` token is used on function parameters it has the opposite effect to "spreading" values, it instead
"compacts" or, more accurately, as [Kyle Simpson](http://getify.me/) puts it, "gathers" the values into an array. Let's take a look at an example.

{%highlight javascript %}
function fullName( ...names ) {
  return names.join( ' ' );
}

console.log( fullName( 'Mary', 'Howard', 'Johnson' ) ); // Mary Howard Johnson
{% endhighlight %}

In the code above, three arguments are passed into `fullName()`. Inside the `fullName()` function, `names` is an array that contains all the arguments passed in, hence `Array.prototype.join()` can be called on `names`.

Let's look at another example with multiple parameters.

{%highlight javascript %}
function logNames( firstName, ...otherNames ) {
  console.log( firstName, otherNames );
}

logNames( 'Mary', 'Howard', 'Johnson' ); // Mary [ "Howard", "Johnson" ]
{% endhighlight %}

As you can see in the code above, the middle and last names were "gathered" into an array while `firstName` was passed along
as an argument.

Pre ES6, programmers used the `arguments` object of a function to get a hold of all arguments passed in. The `arguments`
object was an array like object and was cumbersome to use because it didn't have methods that were on the `Array.prototype`, to get around this, programmers would call the `Array.prototype.slice.call( arguments )` method which would return an array of the arguments passed in.

To visualize what I'm talking about in the above paragraph, take a look at the screenshot below which shows the
properties and prototype of the `arguments` object. As you can see there is a length property but the prototype is not linked
to `Array.prototype`.

![arguments](/images/ES6-rest-and-spread/arguments.png)

Here's a screenshot showing that `Array.prototype.slice.call( arguments )` returns an array whose prototype is linked to `Array.prototype`.

![slice-arguments](/images/ES6-rest-and-spread/slice-arguments.png)

Let's look at a contrived ES5 example of a function that adds an arbitrary number of arguments passed to it.

{%highlight javascript %}
// add an arbitrary number of arguments
function add() {
  var args = Array.prototype.slice.call( arguments );

  var total = args.reduce(function ( previous, current ) {
    return previous + current;
  });

  return total;
}

console.log( add( 1, 2, 3, 4, 5 ) ); // 15

{% endhighlight %}

And here is the ES6 version which is cleaner and concise.

{%highlight javascript %}
function add( ...args ) {
  return args.reduce( ( previous, current ) => previous + current );
}

console.log( add( 1, 2, 3, 4, 5 ) ); // 15
{% endhighlight %}
