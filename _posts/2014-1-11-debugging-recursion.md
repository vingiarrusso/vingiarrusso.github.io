---
layout: post
title: Debugging Recursion
---

Here's a problem that I encountered when I first started writing recursive Javascript.  The code is not overly difficult, involved or long, but the problem is pretty clear.

When trying to find a bug in the code, the first question that you should
ask yourself is, "What evidence do I have that supports my claim that I should be receiving the result that I believe
I should be receiving?"  In other words, point to a place in your code that you believe should be returning you the desired result.  Then, methodically step through the code and find out what is really happening, as opposed to what you think is happening. 

For this problem, my partner and I were writing a simple recursive function that searches through a linked list for a certain target value.  On test one, we pass the spec.  It finds our target right away and returns us the correct answer.  On test two, we fail.  Here's our code below:

``` js
  list.contains = function(target, node) {
    node = node || list.head;
   
    if(target === node.value) {
      return true;
    } else if (node === list.tail) {
      return false;
    } else {
      list.contains(target, node.next)
    }
  };
```
We pondered over our logic, found it to be sound and wondered why it wouldn't give us TRUE on the second test.  We decided to use
debugger to step through our code and find out exactly where it was going and what it was doing. So, we put debugger in the code and end up with this:  (note: I believe debugger only works with chrome dev tools.)

```js
list.contains = function(target, node) {
  node = node || list.head;
  
  debugger;
  if(target === node.value) {
    return true;
  } else if (node === list.tail) {
    return false;
  } else {
    list.contains(target, node.next)
  }
};
```
In our spec tests, we were failing because we received a value of undefined instead of the match on the target we 
passed in.  So, we place debugger before the if statement to test whether or not our if statement was even working correctly.
On the first test through, it successfully enters the if statement and returns true (like expected).  The second test, however, 
it shouldn't find a match the first time, which it doesn't.  We could see debugger skipping through to the else, and
entering our recursive call.  Cool, all is working as intended so far.  

On the second pass, we step through the code, see that it finds the target and returns true.  Okay great, it works, it returns true.  Yet, we're still
getting a result of undefined? How can we be returning true and also be returning undefined at the same time?

What were we missing?  Answer: a return statement before the recursive call. 

```js
list.contains = function(target, node) {
  node = node || list.head;

  if(target === node.value) {
    return true;
  } else if (node === list.tail) {
    return false;
  } else {
    return list.contains(target, node.next)
  }
};
```
I'm the type of person that needs a real life example of these things, so I rationalized it this way:  I'm standing in line, 
lets call it a queue.  I get to the front and realize I'm .50 cents short to buy my coffee.  I turn to the person behind me, 
lets call him Jack.  I say, "Hey man, would you by chance happen to be able to spare .50 cents?".  Jack says "No, sorry dude.".
Alright, well, I really want my coffee and I waited in line all this time. I'm at the front so I'm definitely not leaving now.  So, I say to Jack, "Hey, would you mind asking the person behind you if SHE has .50 cents?".  Jack obliges my request, asks her, gets an answer and then turns around back towards me and continues looking at the long list of coffee drinks above the barista's head.  "Well?", I ask him. "Oh! You wanted me to get the answer AND tell you what that answer was?  Okay, you didn't say that." This is exactly 
the bug in our code.  We asked Jack (our recursive call) to ask the person behind him (node.next) for the 'answer', which he did, but we didnt explicitly tell him to return back to us with an answer.  Jack was holding on to the answer because we didn't ask him to tell us it, so he didn't.  

So, the correct question we should have asked Jack would have been: "Hey, can you ask the person behind you if she has .50 cents and then tell me what she says?"  Lets run this scenario again with that question instead.  Jack asks, gets an answer, turns back to us and says "Yup, she does!".  THANKS JACK! What we do with that information is irrelevant, but now we know, she does in fact have what we were looking for.  