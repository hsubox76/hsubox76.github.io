---
author: Christina Holland
comments: true
date: 2015-04-21 05:04:34+00:00
layout: post
link: http://www.christinahsuholland.com/space-invaders-with-vanilla-javascript/
slug: space-invaders-with-vanilla-javascript
title: Space Invaders with Vanilla JavaScript
wordpress_id: 61
categories:
- Code
---

[![Screenshot 2015-04-20 21.46.30](/images/2015/04/Screenshot-2015-04-20-21.46.30-300x186.png)](http://www.christinahsuholland.com/space_invaders/)

[Play it here!](http://www.christinahsuholland.com/space_invaders/)

For hack night a couple of weeks ago, I followed a [30-minute live coding demo by Mary Rose Cook](https://vimeo.com/105955605) where she made a simple Space Invaders browser game using only vanilla Javascript and HTML - no libraries, no frameworks.
<!-- more -->
I added some minor improvements of my own, mainly:



	
  * Different colors for the bullets, player, and invader

	
  * "Start Game" / "Play Again" buttons, with "Game Over" / "Player Wins" message

	
  * Bullets can't destroy each other

	
  * Limit player's firing rate (it was basically a machine gun as implemented)

	
  * Variation in invaders' color and design

	
  * "Classic" Space Invaders color scheme and old school font


...all while sticking to the philosophy of plain JavaScript of course. I learned some stuff about basic JavaScript/DOM interaction (that you miss when you're always using jQuery) and HTML canvas.

Check it out.

Source code can be found at my [Github repo](https://github.com/hsubox76/space_invaders).
