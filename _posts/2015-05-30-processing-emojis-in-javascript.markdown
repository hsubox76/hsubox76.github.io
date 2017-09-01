---
author: Christina Holland
comments: true
date: 2015-05-30 21:04:38+00:00
layout: post
link: http://www.christinahsuholland.com/processing-emojis-in-javascript/
slug: processing-emojis-in-javascript
title: Processing Emojis In JavaScript
excerpt: If you've ever tried to process emojis in JavaScript, you will understand that they are the devil. Most people dealing with emojis in text data just either pass them through untouched, or remove them as garbage that doesn't need to be evaluated. The second approach...
wordpress_id: 141
categories:
- Code
---

[![Screen-Shot-2014-03-27-at-8.54.37-AM-640x250](/images/2015/05/Screen-Shot-2014-03-27-at-8.54.37-AM-640x250.jpg)](/images/2015/05/Screen-Shot-2014-03-27-at-8.54.37-AM-640x250.jpg)

If you've ever tried to process emojis in JavaScript, you will understand that they are the devil. Most people dealing with emojis in text data just either pass them through untouched, or remove them as garbage that doesn't need to be evaluated. The second approach requires some complex regex work as you can see in [mathiasbynens' emoji-regex repo](https://github.com/mathiasbynens/emoji-regex), which is an entire repo dedicate to one regex.

However, as part of writing a sentiment parser for Twitter recently, I had to actually process and evaluate emojis. Some emojis are normal 16-bit characters that are part of what is called the [Basic Multilingual Plane (BMP)](http://en.wikipedia.org/wiki/Plane_%28Unicode%29#Basic_Multilingual_Plane) - a set of characters commonly recognized by most platforms. Unfortunately, many (most?) emojis are outside of the BMP and take up more than 16 bits.

The JavaScript V8 engine (used in Chrome and Node.js) expects all characters to be 2 bytes (16 bits) max and deals with emojis by pretending they are a sequence of two characters (4 bytes). This makes expressions like (this emoji) === (that emoji) fail, in the same way that ['a','b'] === ['a','b'] would fail.

It's easy to find a lot of solutions out there to either KEEP emojis and make sure they display correctly as images (see Twitter's [twemoji](https://github.com/twitter/twemoji)) or GET RID of emojis so you can process the actual words (see regex above). It's a little trickier to turn emojis into something you can evaluate and compare (their numeric code, for example).

It took a lot of digging, but I finally found a couple of functions inside the twemoji library (not mentioned in the README) that will do the trick.

Courtesy [Twitter/twemoji](https://github.com/twitter/twemoji), this will turn an emoji into a unicode value:

    
    toCodePoint = function(unicodeSurrogates, sep) {
      var
        r = [],
        c = 0,
        p = 0,
        i = 0;
      while (i < unicodeSurrogates.length) {
        c = unicodeSurrogates.charCodeAt(i++);
        if (p) {
          r.push((0x10000 + ((p - 0xD800) << 10) + (c - 0xDC00)).toString(16));
          p = 0;
        } else if (0xD800 <= c && c <= 0xDBFF) {
          p = c;
        } else {
          r.push(c.toString(16));
        }
      }
      return r.join(sep || '-');
    }
    


And this will turn it back, if you need to display it:

    
    fromCodePoint = function(codepoint) {
      var code = typeof codepoint === 'string' ?
            parseInt(codepoint, 16) : codepoint;
      if (code < 0x10000) {     return String.fromCharCode(code);   }   code -= 0x10000;   return String.fromCharCode(     0xD800 + (code >> 10),
        0xDC00 + (code & 0x3FF)
      );
    }
    


This way I was able to compare it to my dictionary of positive and negative emojis - small sample below:

    
    emoticons.positive = {
      '1f601' : 3,
      '1f602' : 3,
      '1f603' : 3,
      '1f604' : 3,
      '1f605' : 3,
      '1f606' : 3,
      '1f609' : 3
      // etc...
    }
    


I created the dictionaries manually by going through [the emoji-to-unicode tables provided by Tim Whitlock](http://apps.timwhitlock.info/emoji/tables/unicode) and judging which ones were positive or negative.
