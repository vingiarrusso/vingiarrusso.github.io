---
layout: post
title: Javascript Gotchas- Hoisting and Scope
---

One of the quirks about Javascript is the way that variables get assigned at runtime.  This often trips up beginners and can even confuse professionals during debugging.  The Javascript interpreter, upon running a script, will do something that basically amounts to a quick scan of the code and decide what variables will be available and what scope they will have.  This is essentially what is called Hoisting.

Lets take a short piece of code and see what's going on here.

Actual code:

```js
  var x = 10;
  console.log(x); // 10

```

Javascript interpretation: 

```js
  var x;
  x = 10;
  console.log(x); // 10

```

We see here that the JS interpreter has scanned through the code without executing anything, and made a list of variables that are available.  In this case, we declared "x" with the "var" keyword, so it becomes hoisted to the top of the scope we're currently in, which in this case we have to assume is global scope.

This brings us to another topic, Scope.  Scope is synonomous with the word "context".  When we speak of the scope of a variable, we are really speaking about what context the variable exists within.  Scope can either be local or global in nature.  Here's a more difficult example of local scope and hoisting that threw me for a loop when I first saw it. 

```js
  var x = 10;
  function myFunc() {
    var x = 5;
      (function myInnerFunc() {
        if (!x) {
          var x = 2.5;
        }
        alert(x);
      })();
    alert(x);
  };

  myFunc(); // will alert 2.5, then alert 5
  console.log(x); // 10

```
How can x be reassigned so many times?  Well, it has to do with scope and hoisting of course.  Remember when I mentioned that scope is the context and hoisting is directly related to scope?  That is the key to this problem.

Here's how JS will interpret this problem: 

```js
  var x; 
  function myFunc() {
    var x; 
    x = 5; 
    (function myInnerFunc(){
      var x;
      if(!x) { //this will evaluate to true when we invoke myFunc,  because x at this point is undefined (a falsy value in JS)
        x = 2.5;
      }
      alert(x); // 2.5
    })();
    alert(x);  // 5
  };

  x = 10;
  myFunc(); 
  console.log(x); // 10

```
Javascript will hoist the variable declarations to the top of their scope.  Functions declared in the style I have done above result in the whole function and its contents being hoisted to the top as well.  If we had declared the function as an expression (ie: var myFunc = function() {...}; ), only the variable name would be hoisted to the top, just like as was done with X.

We can make this give us a different answer with one modification.  What if we remove the keyword "var" from the inner function?

```js
  var x = 10;
  function myFunc() {
    var x = 5;
      (function myInnerFunc() {
        if (!x) {
          x = 2.5;
        }
        alert(x);
      })();
    alert(x);
  };

  myFunc(); //alerts 5, alerts 5
  console.log(x); // 10

```

Removing the keyword var here also removes the hoisting in the myInnerFunc.  We never enter the if statement at the beginning because when JS evaluates !x, it doesn't find an x in local scope so it looks upward in its scope chain, finds a truthy value for x, then evaluates the !x expression to false.

The alert inside the function uses the x in the scope above myInnerFunc, which is 5.

There's a lot more to cover in this topic, but wrapping your head around these basic concepts is important to understanding Javascript.