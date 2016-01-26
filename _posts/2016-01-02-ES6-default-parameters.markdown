---
layout: post
title:  "ES6: default parameters"
date:   2016-01-02 06:19:50
categories: javascript ecmascript6
---

ES6 provides a new syntax to assign default values to function parameters. You might be familiar with this concept from
languages like Ruby, PHP or C++ and it's about time that this was baked into the language.

<!--more-->

One of the ways of checking for default values in ES5 code was to check if the parameter was `undefined`.
Here is a contrived example:

{%highlight javascript %}
function makeRice( type ) {
  var type = type === undefined ? 'white' : type;
  console.log( 'making ' + type + ' rice' );
}

makeRice(); // making white rice
{% endhighlight %}


Let's look at the same example using default function parameters and some ES6 [template strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/template_strings) sugar.

{%highlight javascript %}
function makeRice( type = 'white' ) {
  console.log( `making ${type} rice` );
}

makeRice(); // making white rice

makeRice( 'brown' ); // making brown rice
{% endhighlight %}

### Default value expressions

Default values can be valid expressions and even functions. The contrived code samples below demonstrates this.

{%highlight javascript %}
function calculate( x = 3, y = 2, z = x * y ) {
  console.log( z );  
}

calculate(); // 6
{% endhighlight %}

{%highlight javascript %}
function makeRice( type = defaultRiceType() ) {
  console.log( `making ${type} rice` );
}

function defaultRiceType() {
  return 'white';
}

makeRice(); // making white rice
{% endhighlight %}
