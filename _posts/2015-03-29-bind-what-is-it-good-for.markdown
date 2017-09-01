---
author: hsubox@gmail.com
comments: true
date: 2015-03-29 03:13:06+00:00
layout: post
link: http://www.christinahsuholland.com/bind-what-is-it-good-for/
slug: bind-what-is-it-good-for
title: 'bind() : What is it good for?'
wordpress_id: 19
categories:
- Code
---

We learned about **bind()** today, which is apparently pretty hard to wrap your head around, but I think I got it.  Official documentation here:



<blockquote>[Function.prototype.bind() at Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)</blockquote>



I guess there's two main reasons to use **bind()**:



	
  1. You need to permanently attach a context to a function no matter how or where that function gets called later.

	
  2. You need to permanently attach some default arguments to a function that are always passed in, in addition to any arguments explicitly given to it in future calls.


<!-- more -->
I'm going to focus on reason (1), and here's the problem in more detail: Javascript functions often make use of the keyword _this_, which, confusingly, can refer to a bunch of different things depending on when and how the function is called.  For example, say you make a **Cat** class, with a **feed()** method:


    
    
    // A newly created cat is unfed.
    var Cat = function () { this.fed = false; };
    
    // This function, when called, feeds the cat and sets its "fed" property to true.
    Cat.prototype.feed = function () {
      this.fed = true;
    };



When you make a new **Cat** instance, and call **feed()** on it:


    
    
    var tracy = new Cat();
    console.log(tracy.fed); // logs false
    tracy.feed();
    console.log(tracy.fed); // logs true
    



It works, and the keyword _this_ acts pretty much exactly like you'd expect - it refers to **tracy**, the new **Cat** instance you just made.  Nothing to write home about.

However, there are many strange and wonderful places where this **feed()** function may find itself being called in its journey through your code, like as a callback inside another function, or lost and detached in the global context, or many other exotic places I have not learned about yet.

One common example is it could find itself inside **setTimeout()**:


    
    
    // attempt to call tracy.fed after 1000 ms
    setTimeout(tracy.feed, 1000);
    
    // wait 1000 ms and try
    console.log(tracy.fed); // logs false
    



The problem with **setTimeout** is it pretty much just gets the code inside the **tracy.feed()** function, which is this:


    
    
    function () {
      this.fed = true;
    }
    



So to **setTimeout** these two lines are pretty much identical:


    
    
    setTimeout(tracy.feed, 1000);
    setTimeout(function () { this.fed = true; }, 1000);
    



Timeout just grabs the function code stored in **tracy.feed**.  It doesn't remember what **tracy** is, or even that the function is named **feed**.

It's probably easier to see in the second statement that its _this_ has no idea what it's supposed to refer to.  It doesn't have a context.  So it gets assigned the global context, which in a browser is **window**.  So (I tried this out) it's actually going to create a **window.fed** variable and set it to true.  Which is weird, right?  That's probably not what anyone is trying to do.

Another way a function gets detached from its original context is if it gets assigned to a new variable like so:


    
    
    var independentFeed = tracy.feed;
    independentFeed();
    



This also just pretty much takes the code of the **tracy.feed()** function, leaving the **tracy** context behind, and could potentially call it anywhere without any reference to **tracy**, which it in fact does, in line 2.

This also sets **window.fed** to true, which is again, probably not what anybody wants.

This is where **bind()** comes in!  It basically allows you to stick **tracy** (or whatever original context you want) back onto the function.  So for **setTimeout**, this would work:


    
    
    // tracy WILL be fed after 1 s
    setTimeout(tracy.feed.bind(tracy), 1000);
    



You could fix the other example in a similar way:


    
    
    var independentFeed = tracy.feed.bind(tracy);
    independentFeed(); // tracy.fed will be true
    



When you run **bind()**, your chosen context (in this case **tracy**) gets stuck or perhaps we should say _bound_ (get it) to that bare function code, and the resulting function (that's what **bind()** returns) can be run anywhere, and will use that context for occurrences of _this_ inside of it.

My coding partner and I actually just used it to solve a problem today on our hash table implementation, which really highlighted its usefulness.  Hope this helps any other confused people to see the light.
