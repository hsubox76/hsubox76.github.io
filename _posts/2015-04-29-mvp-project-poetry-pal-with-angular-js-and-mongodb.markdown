---
author: hsubox@gmail.com
comments: true
date: 2015-04-29 06:14:59+00:00
excerpt: Based on a suggestion from the Cracked forums, I made Poetry Pal (name courtesy
  Brandon Ellis). It gives you a template to write different forms of poetry (haiku
  and sonnet are currently available) and checks your syllable count and rhymes (meter
  checking is on the todo list).
layout: post
link: http://www.christinahsuholland.com/mvp-project-poetry-pal-with-angular-js-and-mongodb/
slug: mvp-project-poetry-pal-with-angular-js-and-mongodb
title: 'Poetry Pal: MVP Solo Project with Angular.js and MongoDB'
wordpress_id: 71
categories:
- Code
- Experience
---

**[![Screenshot 2015-04-28 23.16.35](/images/2015/04/Screenshot-2015-04-28-23.16.35-1024x464.png)](http://www.poetry-pal.com)Website:** [www.poetry-pal.com](http://www.poetry-pal.com/)
**Stack:** Angular, Node, MongoDB
**Github: **[hsubox76/poetry-pal](https://github.com/hsubox76/poetry-pal)


We just finished a two day MVP (minimum viable product) sprint where the object was to create a minimum viable product - no frills, some bugs, the basic skeleton in working condition.

Based on a suggestion from [the Cracked forums](http://www.cracked.com/forums/), I made Poetry Pal (name courtesy Brandon Ellis). It gives you a template to write different forms of poetry (haiku and sonnet are currently available) and checks your syllable count and rhymes (meter checking is on the todo list).

<!-- more -->

[![Screenshot 2015-04-28 22.44.34](/images/2015/04/Screenshot-2015-04-28-22.44.34-300x127.png)](/images/2015/04/Screenshot-2015-04-28-22.44.34.png)

Try the app [here](https://poetry-pal.herokuapp.com/index.html#).

You can see the code [here on Github](https://github.com/hsubox76/poetry-pal).

The front end is done in Angular, with Bootstrap for styling. The word checks go to a server running Node with Express, which queries a Mongo database containing approximately 120,000 words from the [Moby pronunciation dictionary](http://icon.shef.ac.uk/Moby/mpron.html).

It definitely puts the "minimum" in "minimum viable product" though. Here's what's yet to be done:



	
  * Needs to recognize capitalization variants (database should be re-ingested to be all lowercase)

	
  * Needs to better recognize plurals and their effects on syllable count

	
  * Needs to recognize verb conjugations and their effects on syllable count

	
  * Needs to work with punctuation.

	
  * Needs to work with contractions.

	
  * Database lookup is slow (checking for plural variants involves running multiple queries, this is probably not the best approach).

	
  * Needs to be able to check meter (pronunciation is in the database).

	
  * Highlight words not found or words that required AI guesses in a different color. (May need to exchange text fields for editable divs.)

	
  * Need a system to allow users to add words not found in the dictionary. (This is a big job - regular users can't be expected to be versed in obscure phonetic symbols, a user-friendly series of questions needs to be implemented to help them explain how the word should be pronounced.)

	
  * Add more poetry forms (limericks, etc.)


I liked the opportunity to explore Angular, I think it's a pretty neat framework with a lot of possibilities. Together with Bootstrap, it made it easy to put together a useful and good-looking user feedback interface.

The hardest part was actually ingesting the free dictionary data into the database, which isn't really that surprising. Large data ingests are always full of snags, especially when the data is free.

The dictionary didn't come with syllable division, which is a pretty tricky problem, and it was full of slashes, which confused the file stream reading functions. On top of that, the size of the data set (170,000 words raw, I filtered some out) meant I had to figure out how to read and input the data as a stream to avoid running out of memory. I could have split it into multiple files but I felt like that would have been a copout. You can't always cheat your way out of your inefficiency problems.

Anyway, good experience, I felt like I managed my time well enough and reached a good stopping point by the time we had to do demos, which I guess was the biggest part of the goal.
