---
author: Christina Holland
comments: true
date: 2017-09-07 00:17:46+07:00
layout: post
link: http://www.christinahsuholland.com/getting-https/
slug: getting-https
title: HTTPSing Your Website
excerpt: Getting HTTPS on your site is all the rage these days, for some good reasons. Google's search rankings take into account whether a site is HTTPS or not. Google Chrome is going to start telling people your site is "Not secure" under certain circumstances, starting in Oct 2017. (It already shows a less obtrusive "i" symbol.)

categories:
- Code
- Experience
---

## Why HTTPS?

Getting HTTPS on your site is all the rage these days, for some good reasons.

### Google Pressure

Google's search rankings [take into account whether a site is HTTPS or not](https://www.sangfroidwebdesign.com/search-engine-optimization-seo/google-https-ranking/).

Google Chrome [is going to start telling people your site is "Not secure"](https://blog.chromium.org/2017/04/next-steps-toward-more-connection.html) under certain circumstances, starting in Oct 2017. (It already shows a less obtrusive "i" symbol.):

![google https](/images/googlehttps.png){:height="249px" width="660px"}

[Firefox is starting to roll out the same HTTP-shaming functionality](https://blog.mozilla.org/security/2017/01/20/communicating-the-dangers-of-non-secure-http/). Pretty soon, any site that doesn't have HTTPS is going to come across as alarming and sketchy when a visitor navigates there in any popular browser.

### Inherent Reasons

And it's not just mean Google being arbitrary.  A secure connection is a pretty big deal.  Obviously if you're getting people's credit card information, email addresses, passwords, and that sort of thing, you can see why.

But even a static site that doesn't take any user input has security risks.  Anyone that can get in the middle of your site sending content to your intended user can cause trouble.

- Comcast, Starbucks, hotel wifi, whatever permanent or temporary ISP people use to connect to the internet - these can pop in and add anything they want onto the page you're sending down, which turns out to be mostly ads, if it's a big corporation.  If you've promised your users no ads, or no ads of a certain kind, this is going to make you look like a liar.

- It could be a lot worse than a big clumsy company putting its dumb ads on your page without your knowledge - it could be some jerk thief adding a fake button or link that directs your unsuspecting user to some phishing site.  A button they will happily click on because it's on your page, and they trust you.

### What HTTPS Doesn't Do

HTTPS doesn't prove your site is your site.  It's not a Twitter verified checkmark proving arbys.com is really Arby's official website.  There is a special type of certificate that is supposed to do this, but [most users don't know about it and don't realize the regular green lock isn't it](https://www.troyhunt.com/on-the-perceived-value-ev-certs-cas-phishing-lets-encrypt/).

It doesn't prove the site isn't owned by terrible criminals, it just proves that you can communicate securely with the terrible criminals or trusted company or whoever it is.

## How HTTPS?

"Well, now," you're probably saying, "I simply MUST get me some of that HTTPS."  How do you do this?  Does it cost a ton of money?  No, it's totally free.

### Get A Free Certificate From Let's Encrypt

[Let's Encrypt](https://letsencrypt.org/) is a nonprofit organization dedicated to making the internet safer or and that kind of thing.  It's very reputable.  Don't fall for companies saying you have to pay money to get a better quality certificate or whatever.

They have instructions on their website, but as they say on their site, it's easiest to go through your hosting provider if they do provide some kind of Let's Encrypt integration.

I had two types of sites to encrypt, a Github Pages site and an Amazon S3 static site.  The Github Pages site eventually became a S3 site because I got fed up with Github Pages, but more on that later.

If you have a nice hosting provider with a Let's Encrypt integration, they might do everything for you.  I did not.  I did have a nice domain management service called [DNSimple](https://dnsimple.com/) which registers my domains and manages my DNS records.  DNSimple did provide Let's Encrypt integration.

DNSimple automated as much of the job as it could.  The most annoying part of it involved pointing a path on my domain at a custom file for Let's Encrypt to look at, so it could verify I owned the domain and could point it at things, and DNSSimple did all of that.

But DNSSimple didn't actually host my site so it couldn't actually put the certificates on it once I got them.  About 30 minutes after I clicked the button for DNSimple to go get me a cert, it emailed me that the cert was ready.

### Import The Cert Into Amazon S3

I went to DNSimple, and again, there are a lot of options to automatically install the certs on a bunch of common hosting providers, none of which was Amazon or Github.  But it turned out to be pretty simple in Amazon's case.  DNSSimple gave me some links to cert files (.crt, .pem, .key) to download and manually put on my hosting service.

I just found the "Amazon Certificate Manager" in my AWS console, and navigated up to the Amazon Certificate Manager home screen (it automatically seems to send you to the "Request a Certificate" screen because they want you to get certs through them I guess).  Then I clicked "Import a Certificate".

![ACM image 1](/images/aws1.png)

Then the "Import a Certificate" screen comes up, with this bit in it:

![ACM image 2](/images/aws2.png)

The stuff to put in those boxes comes from the certificate files I downloaded, which are just text files and can be opened in text editor.  You don't need all the files, just look for the stuff the form is asking for.

### Set Up Amazon CloudFront for My S3 Site

Next, I needed another Amazon service, CloudFront, which seems to be exactly like Cloudflare except it's Amazon and 4 letters are different.  It delivers your content quickly to users in all locations, protects you from DDOS, and most importantly it lets you put an HTTPS certificate on your site.

You click the "Create Distribution" button, select "Web," and in "Origin Domain Name," put your S3 site's **web address**.  NOT the S3 bucket name.  You've got to do that so it'll serve files through the web server and not like a filesystem or whatever.

Right: ```mysite.s3-website-us-east-1.amazonaws.com```

Wrong: ```mysite.s3.amazonaws.com```.

Other important bits:

- "Viewer Protocol Policy": Redirect HTTP to HTTPs. Why are you going to all the trouble to set up HTTPS if any poor sucker who forgets to type "https" won't get it?

- "Alternate Domain Names (CNAMEs)": Your domain name, the one that directs to this page.  Plus a wildcard prefix for "www." and whatnot.  So like: ```adequite.com, *.adequite.com```.

- "SSL Certificate": All your work has built to this.  Select the certificate you earlier imported to Amazon Certificate Manager.

When you're done, click "Create Distribution" and wait 30 minutes or so for CloudFront to set up and do its thing.

### Point Your Domain At CloudFront

CloudFront should give you some jumbled up url to reach your site, (example: https://dx0anz7l0ywxb.cloudfront.net/).  Go to your domain name manager and point it here with an CNAME or record.  (You can't point a CNAME record at your root domain, so "www.adequite.com" is ok but "adequite.com" isn't.  You can use an ALIAS record if you want someone typing your root domain to be directed to your site.)

### Try To Import Cert To Github Pages

In one sense, Github Pages makes https really easy in that it automatically comes with the site (you just have to check a box).  Unfortunately, this only applies if you use the Github Pages url (myusername.github.io).  Which I guess is fine, github.io has a lot of street cred, I guess I could have stuck with that.

But I didn't.  I had a custom domain name that I wanted to direct to my Github Pages page and then things got tricky.

Requesting the cert for my Github Pages site actually did not work.  Again I had to get a Cloudf**** service (I used Cloudflare this time) in front of it first.  Once I did that, the cert came through, and I was able to put it on Cloudflare.  One drawback was I couldn't use "Full (Strict) mode," but only "Full" mode.

[Cloudflare has a more detailed blog post] on how to do this.

### Get Fed Up With Github Pages

After having to do all these workarounds with my custom domain name, I was getting a little annoyed with Github Pages.  Then suddenly it started to fail to rebuild my CSS files (only on the server, it worked fine locally).  I was not receiving any error email notifications and there was no way to access a console or anything like that to get any clue as to what was going on, and none of the docs addressed my problem, so I was like, screw it.  I moved the whole site over to Amazon S3 and redid the HTTPS thing as I did with the other S3 site.

## Et Voila

And here's the new site with those nice green locks.  Hail Amazon I guess.

![Green Lock On Chrome](/images/greenlock1.png)

![Green Lock On Firefox](/images/greenlock2.png)
