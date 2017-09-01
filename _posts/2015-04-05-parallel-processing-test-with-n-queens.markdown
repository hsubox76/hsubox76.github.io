---
author: hsubox@gmail.com
comments: true
date: 2015-04-05 06:14:17+00:00
layout: post
link: http://www.christinahsuholland.com/parallel-processing-test-with-n-queens/
slug: parallel-processing-test-with-n-queens
title: Parallel Processing Test With n-Queens
wordpress_id: 51
categories:
- Code
- Project
---

[![undefined.jpg](/images/2015/04/undefined-e1428212923980.jpg)](http://christinahsuholland.com/nqueens/webworker/webworker.html)

We had an assignment where we came up with an algorithm to solve the classic [n-Queens puzzle](http://en.wikipedia.org/wiki/Eight_queens_puzzle).

(The problem in brief: Given an n x n chessboard, place n queens on it so that none of them can attack each other.  For any given n, how many configurations are there where this is true?)

This is obviously a computer problem because by n = 13, for example, there are 73,712 combos, which nobody should be figuring out by hand.  By the time n hits the mid-teens, it starts taking a few minutes to calculate on regular computers.

<!-- more -->

The algorithm we came up with (I worked with classmate **Benjamin Zarzycki**) could probably use a fair amount of optimization (we didn't take symmetry into account for example, so that would halve the time) but we're using it as is to try out parallel processing through [HTML5 web workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers).

Web workers are parallel processes you can spawn off through your browser, either to run intensive tasks in the background without interfering with your main browsing experience, or to get a task done faster by splitting it into different parts which can be processed at the same time by different cores on your computer.

Here's our [webworker page](http://christinahsuholland.com/nqueens/webworker/webworker.html), feel free to try out different n's and different numbers of web workers on your own.  I would start at 12 or 13 and slowly work up.  Also most people will see improvement going from 1 to 2 workers, but I think you might need more than 4 cores to do better (I think one is already saved for the main process).  I have a Macbook Pro with a Core i5 processor and it doesn't make much difference going from 2 workers to 3 workers and up.

But I'm not totally sure how distribution among cores work - depends on the browser I guess?  And how it wants to divvy things up.  So let me know if you find out more from your results.

And almost forgot, [source code here](https://github.com/hsubox76/n-queens)!
