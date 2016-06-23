---
author: hsubox@gmail.com
comments: true
date: 2015-04-22 02:51:46+00:00
layout: post
link: http://www.christinahsuholland.com/dumb-express-js-mistakes/
slug: dumb-express-js-mistakes
title: Dumb Express.js Mistakes
wordpress_id: 68
categories:
- Code
- Experience
---

My excuse here is going to be that I'm new to Express.js, but hopefully this will help someone else who is also new.

I spent a couple of hours banging my head on a pretty dumb mistake due to a misunderstanding of Express middleware.  An Express middleware function is a function that can be run in the middle, between your route and your explicitly written callback, taking data from the function before it, acting on it, and passing the data forward.

You can stick the middleware function right inside your route call, or you can mount middleware to be used on all routes using app.use(middleware goes here) before you define the routes.

Unfortunately, if you're not used to the concept of middleware, you see a bunch of app.use() statements in your code and in your mind, these are just a bunch of "require" statements using some other terminology, where the order doesn't much matter.
<!-- more -->
Well, the order does matter, as it turns out.  It determines the order the middleware is chained together in your routes.  Some middleware is dependent on previous middleware in the chain having done something.  Take a look at this example:


    
    
    app.use(passport.initialize());
    app.use(passport.session());
    app.use(session({ secret: 'foobar' }));
    



What's wrong with this picture, other than a really insecure choice of "secret" for the session?

Well, if you're not familiar with [Passport](http://passportjs.org/guide/oauth/), it's an authentication middleware module.  For storing the state of any given user/site authentication, it uses a session.  Which won't exist until the session middleware sets it up.  I think you can probably see the problem here.  The session middleware is on the last line, after the passport middleware.  The passport middleware has no session to store stuff in.

I banged my head on this for a couple of frustrating hours until I finally happened upon an example that had a comment about how order matters.  So then I did this:


    
    
    app.use(session({ secret: 'foobar' }));
    app.use(passport.initialize());
    app.use(passport.session());
    



And the app ran fine.  And I was simultaneously happy and angry.  But I learned something important about middleware, and not making assumptions.

Another corollary of this is never to put routes before "use" statements, at least if you are intending for the route to use that middleware.

This will lead to nothing but tears:


    
    
    app.get('/', function (req, res) {
      req.session.user = 'Bob';
    });
    
    app.use(session());
    



"Why is req.session.user empty?" you will cry, as you pull at your hair.  "I put 'Bob' in there, it's right there!"  Then after some time you will realize that your session middleware doesn't come into play until after you have tried to put the string 'Bob' into a nonexistent session.

Anyway, don't do that.  Stay in school, stay off drugs, and put your Express middleware in the right order.
