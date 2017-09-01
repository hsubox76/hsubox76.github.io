---
author: Christina Holland
comments: true
date: 2015-06-11 06:47:22+00:00
excerpt: 'Crowd Parser was our final thesis project at Hack Reactor.  Our team of
  3 had 3 weeks to work on this project, so we decided to get a little ambitious.  The
  key things we wanted to work on were: (1) Big data, (2) Natural language processing
  (NLP), (3) Large-scale data visualization...'
layout: post
link: http://www.christinahsuholland.com/crowd-parser-thesis-project/
slug: crowd-parser-thesis-project
title: Crowd Parser - Thesis Project
wordpress_id: 210
categories:
- Code
- Experience
- Project
---

[![cp_homepage](/images/2015/06/cp_homepage.png)](https://crowdparser.org)


**Website:** [crowdparser.org](https://crowdparser.org/)
**Stack:** Angular, MySQL, Three.js, Node, Express
**Github:** [crowd-parser/crowd-parser](https://github.com/crowd-parser/crowd-parser)
**Team Members:** Michael Cheng, Christina Holland, Cooper Buckingham




Crowd Parser was our final thesis project at Hack Reactor.  Our team of 3 had 3 weeks to work on this project, so we decided to get a little ambitious.  The key things we wanted to work on were:






	
  1. Big data

	
  2. Natural language processing (NLP)

	
  3. Large-scale data visualization


So the idea we came up with was to process tweet data from Twitter (big data), run sentiment analysis on it (NLP), and display the results in 3D (data visualization).  The bulk of my work ended up being the 3D data visualization in Three.js, where the work moved quickly at first, and then got slower and slower as it got closer to completion.  Less big steps forward, more bug fixes with the two steps forward, one step back and all.

I was a little disappointed I didn't get to put more work into the NLP side of things, but I did implement a pattern-based negation recognition algorithm and an emoji-based sentiment parser.

The overall structure of the app was this - we stored up a database of what ended up being over 6 million tweets by picking every 3rd tweet or so off of Twitter's API stream.  (We had to store them because otherwise you can only get 100 tweets at a time from a REST keyword search to Twitter's API, and recent ones only, and our goal was for users to search a large representative number of tweets going back at least more than a day.  So we stored them ourselves from the stream.  We didn't get every tweet but we thought by picking every nth tweet, it would at least be statistically representative.)

Then, we ran different algorithms to analyze sentiment.  Our "base" layer just checked words in the tweet against a dictionary of words rated "positive" or "negative" and assigned the tweet a score based on the sum of all the positive and negative words found.  A subsequent layer checked to see if any of the words were negated ("not bad" should count as positive even though "bad" is negative).  Another layer checked emojis against an emoji dictionary in the same way the "positive" and "negative" words were scored.  A fourth layer checked for slang words not covered in common dictionaries.  I was also working on a native JavaScript tree parser that would have more robustly determined negation, and might have helped us ensure the "positive" and "negative" words used in scoring were in fact referring to the keyword.  Unfortunately I ran out of time due to the demands of the 3D display.

The 3D display was the next step after the "layers" were processed.  It displayed each layer of sentiment parsing as an actual visual layer, like a sheet of paper, with every tweet represented by a colored square - blue for positive, red for negative, white for neutral.

[![cp_display_1](/images/2015/06/cp_display_1.png)](/images/2015/06/cp_display_1.png)



Layers start out slightly separated but can be "flattened" by the user to stack the semi-transparent squares on top of each other and get a collective score for each tweet by looking at the shade of the resulting color.  Layers can also be toggled on and off to see how results change with different combinations of layers.

The most difficult thing to implement was levels of detail.  The goal was for the user to be able to zoom in and out seamlessly, with the tweets becoming less detailed as you zoomed out (to save memory) and more detailed as you zoomed in, to the point where you could read an individual tweet.

[![cp_display_4](/images/2015/06/cp_display_4.png)](/images/2015/06/cp_display_4.png)

[![cp_display_3](/images/2015/06/cp_display_3.png)](/images/2015/06/cp_display_3.png)



I managed this with several levels of detail (LOD).  The highest is when you are zoomed all the way in.  Each tweet is represented by an HTML div element rendered in 3D using Three.js's CSS 3D renderer.  As you zoom out, each div changes to a Three.js plane geometry object.  As you zoom out further, the 16 geometry objects in a 4x4 square merge into one object (while still appearing as 16 squares), and as you zoom out still farther, every 16x16 square merges as well.  This causes some stuttering during the merges, but the payoff is that when you are zoomed out all the way and looking at 20,000 tweets, panning around, as well as flattening, and separating layers, goes as quickly as when you are zoomed in on a handful of tweets.

I think it is an understatement to say that this was not easy.  I managed to get it to work up to at least 20,000 tweets, as mentioned above, but the casualty was my other pet project, the parse tree, which did not make it into Crowd Parser by code freeze day.  I spun it off as a personal repo and will continue to work on it.




