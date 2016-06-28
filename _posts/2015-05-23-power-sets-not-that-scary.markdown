---
author: hsubox@gmail.com
comments: true
date: 2015-05-23 21:23:32+00:00
layout: post
link: http://www.christinahsuholland.com/power-sets-not-that-scary/
slug: power-sets-not-that-scary
title: 'Power Sets: Not That Scary'
wordpress_id: 131
categories:
- Code
---


The power set problem is one of those algorithms that turns out to be a bit trickier than they appear on the surface. Like the five stages of grief, there are 3 common stages for being assigned a power set problem.



	
  1. "This looks pretty simple, it'll probably take about 10 minutes."

	
  2. "This problem is a hell from which I will never escape."

	
  3. "Oh, actually it was pretty simple


If anyone is on stage 2, I hope this post will help get you to stage 3. If anyone is on stage 1, try to get to stage 2 first, it will build character. Or if you are really smart, just skip to stage 3 on your own.

The basic problem is that you are given a string (say, "abc") which you can look at as a set of letters, more or less, and your job is to return the [power set](http://en.wikipedia.org/wiki/Power_set) of that set of letters. The power set is a set of all subsets of the original set ("abc" in this case), including the empty set ("") and the original set itself ("abc").

So the power set of "abc" would be:
"", "a", "b", "c", "ab", "ac", "bc", "abc"

Note that:



	
  * No duplicate items allowed: the definition of set means no duplicates. No "aab" or the like.

	
  * Order doesn't matter: "ab" and "ba" are the same set, only one should go in the power set (see "no duplicates" rule)

	
  * You want to sort the string first (explanation later)


I banged my head on this for a while with no success until I grabbed a piece of paper and drew a tree. That's when it all made sense. Some people start this problem from the top down (the full set) and remove things. I started at the bottom (single letters) and built sets from them. So first I busted out each letter from the set and made them a starter seed from which new trees would spring.

[![powerset_tree_1](/images/2015/05/powerset_tree_1.png)](/images/2015/05/powerset_tree_1.png)

Then in the next round (the next level of the tree), each letter adds a second letter to itself. The only two rules are



	
  1. the second letter has to come after the first letter (in a sorted set, this will prevent you from getting the same combo in a different order)

	
  2. the second letter can't be the same as the first letter, or any of the other letters you've collected in your particular combo so far (this prevents duplicates)


[![powerset_tree_2](/images/2015/05/powerset_tree_2.png)](/images/2015/05/powerset_tree_2.png)

If the string was "aabcd" or something, the "a" branch would try to add the second a (to get "aa") but the duplicate rule would stop it.

So you keep following those rules and no matter how many duplicates you might have, you eventually get this:

[![powerset_tree_3](/images/2015/05/powerset_tree_3.png)](/images/2015/05/powerset_tree_3.png)

And there's all the sets to put in the power set, except the empty one.  So here's the code, step by step:


    
    var powerSet = function(str){
      // prepopulate it with the empty set
      var results = {"":true};
    



I'll be storing the results as keys in an object, because I don't have to worry about storing the same thing twice.  If I do, it will overwrite the other key instead of duplicating it.  So the first thing that goes in that object is the empty set ("").


    
    
      // str to array
      var array = str.split("");
      // array back to string
      str = array.sort().join('');
    



Then I sort the string by turning it into an array, sorting it, and making it a string again.  I'm not sure if turning it back into a string was necessary but I did it.


    
    
      var subSets = function(comboSoFar, indexOfLast) {
        var combo;
        for (var j = indexOfLast + 1; j < str.length; j++) {
          if (comboSoFar.indexOf(str[j]) < 0) {
            combo = comboSoFar.concat(str[j]);
            results[combo.join("")] = true;
            subSets(combo, j);
          }
        }
      };
    



This function within the function is the meat of the algorithm.  It takes the combo so far (one character long at the first level, two at the second, etc.) and creates new branches for each possible letter added to it.  Branches are only created for letters whose position in the string comes after the string position of the last letter in the combo.  So "bc" yes, "ba" no.  Branches are also only created if the additional letter doesn't already exist in the combo.  So "abc" yes, "abb" no.  If the rules are met, it adds the combo to the results object (if it's already there, no problem, you don't get duplicate keys in objects) and starts off another level of branching by calling subSets on the new combo.  (Note that if it wasn't sorted, you could get "ab" as well as "ba" and you'd have to put some kind of manual check at this point to make sure you're not adding duplicates.)


    
    
      for (var i = 0; i < array.length; i++) {
        results[str[i]] = true;
        subSets([str[i]], i);
      }
    



This part kicks off that recursive function by running it on each character of the string.


    
    
      return Object.keys(results);
    



At the end, it returns the keys of results object.  (Object.keys() returns the keys of that object as an array.)

And that works as far as I can tell!  Let me know if I missed anything.
