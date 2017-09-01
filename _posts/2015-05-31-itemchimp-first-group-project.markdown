---
author: hsubox@gmail.com
comments: true
date: 2015-05-31 22:39:55+00:00
excerpt: ItemChimp was a weeklong group project - our first group project at Hack
  Reactor.  This app allows you to compare reviews for products on online stores -
  specifically Wal-Mart and Best Buy so far.  Usage is simple - you search for a product
  by keyword, and the app will bring up keyword search results from both stores...
layout: post
link: http://www.christinahsuholland.com/itemchimp-first-group-project/
slug: itemchimp-first-group-project
title: ItemChimp - First Group Project
wordpress_id: 176
categories:
- Code
- Experience
- Project
---

[![ic_homepage](/images/2015/05/ic_homepage.png)](/images/2015/05/ic_homepage.png)


**Website:** [itemchimp.azurewebsites.net](http://itemchimp.azurewebsites.net/)
**Stack:** React.js, D3, Node, Express, MySQL
**Github:** [PebbleFrame/item-chimp](https://github.com/PebbleFrame/item-chimp)
**Team Members**: Christina Holland, Michael Cheng,
Jeff Peoples, Vinaya Gopisetti




ItemChimp was a weeklong group project - our first group project at Hack Reactor.  This app allows you to compare reviews for products on online stores - specifically Wal-Mart and Best Buy so far.  Usage is simple - you search for a product by keyword, and the app will bring up keyword search results from both stores (plus a price chart, implemented by Michael Cheng - I did the other D3 chart you'll see later).




[![ic_price](/images/2015/05/ic_price.png)](/images/2015/05/ic_price.png)




If you select a product from the search results, the app will look to see if the other store has that same product (with a UPC search).  If so, it will show side-by-side columns with reviews from both stores.




[![ic_reviews_2_across](/images/2015/05/ic_reviews_2_across.png)](/images/2015/05/ic_reviews_2_across.png)




At top is a D3 graph showing each review as a colored dot.  The colors indicate which product/store combo the review is for, the size indicates the length of the review, and the x position indicates the star rating (1-5).  This gives you a quick view of which stores rate the product more highly, whether reviews follow a bell-curve distribution or whether they are polarized at both ends, and other interesting info that a simple average won't give you.




There's also an interface on the right that lets you pick any one of the other search results to compare to those two columns.  Once you pick one, it becomes a third column, and also adds a third set of dots to the D3 chart.




[![ic_reviews_3_across](/images/2015/05/ic_reviews_3_across.png)](/images/2015/05/ic_reviews_3_across.png)




As you can see, people are a little more iffy on the refurbished iPod than on the new ones (although admittedly it isn't much of a sample size).  You can also remove columns by clicking on the red x, and add different products from the "Choose another product to compare" box, which reappears once you have less than 3 columns again.




My contribution was the animated D3 reviews chart and the rest of the reviews page in general.  I was pretty new to D3 and completely new to React going in, but have taken a liking to both.




I also helped figure out the complex routes required for getting the review data from the API.  Vinaya Gopisetti was the point person on the server backend and the API interaction, but we all participated in figuring out the numerous challenges involved with getting the data we wanted from the API.  These included the fact that Amazon (one of the original stores we wanted to include) does not give you review data in its API but an iframe url that links to its review page, rendering it unusable for our app.  The other big issue was that review and product APIs for both Best Buy and Wal-Mart use separate REST routes, and we had to chain together up to 4 calls to connect review data to product data (like UPCs) and sometimes product data (UPCs again) to other product data (store-specific product IDs like SKU for Best Buy or ID for Wal-Mart) back to review data for the other store.




This was part of our "Greenfield" project, which meant that we would originate the idea, finish a MVP (minimum viable product) more or less, and leave some features for a second group to implement.  So there are some incomplete features we left room for, such as allowing users to leave their own ItemChimp reviews (which would then be displayed along with store reviews), save their searches, and follow other ItemChimp users.  The groundwork for this involved a user account and authentication system, which Jeff Peoples built and completed, then mocked up future not-yet-complete functionality (following users, editing profile) in the Dashboard.  (Jeff also managed the automatic deployment of the site onto a Digital Ocean server.)
