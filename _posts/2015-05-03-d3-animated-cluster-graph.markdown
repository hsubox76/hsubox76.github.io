---
author: Christina Holland
comments: true
date: 2015-05-03 00:40:42+00:00
layout: post
link: http://www.christinahsuholland.com/d3-animated-cluster-graph/
slug: d3-animated-cluster-graph
title: D3 Animated Cluster Graph
wordpress_id: 80
categories:
- Code
---








(The data in the demo above is randomly generated dummy data and changes every time you refresh.)

As part of our Greenfield project (our first group project), I'm working on using D3 to display our data (reviews collected from different retail sites) in a cool way.  My idea for this chart was that each dot represents a single review.  The color indicates what site it's from, the size indicates how long it is, and the X position indicates how many stars the reviewer gave the product (on a 0-5 scale).

<!-- more -->

I thought it would be interesting to see the different shapes the graph took for different products.  For example, two products might have an average 3 star rating, but one has more of an hourglass distribution (very polarizing, a lot of 1 star and 5 star reviews), and one might look more like a ball (almost everyone agrees it was a 3 star product).

I started with this [multi-foci force layout example](http://bl.ocks.org/mbostock/1021953) and made some modifications.

My "charge" force depends on the size of the dot, so bigger circles exert more repel force than little ones.  Obviously there are more foci (one per 0.5 star, so there's foci at 0, 0.5, 1, 1.5, etc.).  I also had to make some minor tweaks as all the review dots appear at once and move into place at the same time, instead of appearing one at a time and moving into position.

Finally, I added a tooltip that appears when you hover over a dot and shows you a preview of the review data (username, review title, a few words from the review).

Code here if you want to see it (warning, kind of long):

JavaScript:

    
    
    var storeData = [
      {name: "Amazon", color: "steelblue"},
      {name: "WalMart", color: "darkorange"},
      {name: "Best Buy", color: "darkseagreen"},
    ];
    
    var dataGen = function () {
      results = [];
      for (var i = 0; i < 20; i++) {
        var obj = {};
        obj.numLines = Math.floor(Math.random() * 39) + 20;
        obj.stars = Math.round(Math.random() * 10)/2;
        obj.site = storeData[Math.floor(Math.random() * 3)];
        obj.username = 'User' + i;
        obj.reviewTitle = 'NO CATS INCLUDED';
        obj.review = "I thought this product was crap.  It didn't come with any cats.  \
        I require cats in all my products and I plan on writing a very angry letter to \
        the manufacturer as a result of not having my cat needs met.";
        obj.reviewStart = obj.review.slice(0, 110) + "...";
        results.push(obj);
      }
      return results;
    };
    
    var data = dataGen();
    
    var legendData = ['0 stars', '1 star', '3 stars', '4 stars', '5 stars'];
    
    var width = 500,
      height = 300,
      yOffset = 50;
    
    
    // x based on star rating
    var x = d3.scale.linear()
              .domain([0, 6])
              .range([0, width]);
    
    var fociX = d3.scale.linear()
              .domain([0, 6])
              .range([100, width-50]);
    
    var fociGen = function (numFoci, x) {
      var results = [];
      for (var i = 0; i < numFoci; i++) {
        results.push({x: fociX(i+1)/2, y: 150});
      }
      return results;
    };
    
    var foci = fociGen(11, x);
    
    var force = d3.layout.force()
      .gravity(0)
      .links([])
      .nodes(data)
      .charge(function(d) { return d.numLines * -1.5; })
      .size([width, height]);
    
    var chart = d3.select(".chart")
      .attr("width", width)
      .attr("height", height);
    
    var circle = chart.selectAll("g.node")
        .data(data)
      .enter().append("g")
        .classed("node", true)
        .attr("transform", function(d, i) { 
          return "translate(" + (x(d.stars)+ d.numLines) + "," + yOffset + ")";
        });
    
    circle.append("circle")
      .attr("cx", 0)
      .attr("cy", 0)
      .attr("r", function(d) { return d.numLines/2; })
      .style("fill", function(d) { return d.site.color; })
      .style("stroke", "white")
      .style("stroke-width", 2)
      .style("stroke-opacity", 0.5);
    
    circle.append("text")
      .attr("x", 0)
      .attr("y", 0)
      .attr("dy", ".35em")
      .text(function(d) {return d.stars;});
    
    var legend = chart.selectAll("g.legend")
      .data(legendData)
      .enter().append("g")
      .classed("legend", true)
      .attr("transform", "translate(0, " + (height-25) + ")");
    
    legend.append("text")
      .attr("x", function(d, i) { return x((i*1.25)+0.3); })
      .attr("y", 0)
      .text(function(d) {return d});
    
    var storeLegend = chart.selectAll("g.storeLegend")
      .data(storeData)
      .enter().append("g")
      .classed("storeLegend", true)
      .attr("transform", "translate(20,10)");
    
    storeLegend.append("rect")
      .attr("x", function (d,i) { return i*100; })
      .attr("y", 5)
      .attr("width", 80)
      .attr("height", 25)
      .style("fill", function (d) { return d.color; });
    
    storeLegend.append("text")
      .attr("x", function (d,i) { return i*100 + 5; })
      .attr("y", 18)
      .attr("dy", "0.35em")
      .text(function(d) { return d.name; } );
    
    var nodes = chart.selectAll("g.node");
    
    
    var ttOffset = 10;
    var ttWidth = 220;
    var ttHeight = 105;
    
    var tooltip = d3.select(".d3-container")
      .append("div")
      .style("width", ttWidth + "px")
      .style("height", ttHeight + "px")
      .classed("hoverbox", true);
    
    tooltip.append('div')
      .classed("username", true);
    
    tooltip.append('div')
      .classed("reviewTitle", true);
    
    tooltip.append('div')
      .classed("reviewText", true);
    
    nodes.on('mouseover', function(d) {
      var mouseLoc = d3.mouse(this.parentNode);
      if (mouseLoc[0] + ttOffset + ttWidth > width) {
        mouseLoc[0] = mouseLoc[0] - ttOffset*2 - ttWidth;
      }
      if (mouseLoc[1] + ttOffset + ttHeight > height) {
        mouseLoc[1] = mouseLoc[1] - ttOffset*2 - ttHeight;
      }
       tooltip
            .style("display", "block")
            .style("left", (mouseLoc[0]+ttOffset)+"px")
            .style("top", (mouseLoc[1]+ttOffset)+"px")
            .transition()
            .duration(200)
            .style('opacity', 1);
      tooltip.select(".username")
        .text(d.username + " on " + d.site.name);
      tooltip.select(".reviewTitle")
        .text(d.reviewTitle);
      tooltip.select(".reviewText")
        .text(d.reviewStart);
    });
    
    nodes.on('mouseout', function(d) {
      tooltip.transition()
        .duration(200)
        .style('opacity', 0)
        .each('end', function () {
          tooltip.style("display", "none");
        });
    });
    
    force.start();
    
    
    force.on("tick", function(e) {
      var k = .1 * e.alpha;
    
      data.forEach(function(o,i) {
        o.y += (foci[o.stars*2].y - o.y) * k;
        o.x += (foci[o.stars*2].x - o.x) * k;
        chart.selectAll("g.node")
          .attr("transform", function(d) { 
            return "translate(" + d.x + "," + d.y + ")";
          });
      });
    });
    



Stylesheet:

    
    
    .chart {
      border: 1px solid gray;
    }
    
    .chart text {
      fill: white;
      font: 10px sans-serif;
      text-anchor: middle;
    }
    
    .legend text {
      fill: gray;
      font: 16px sans-serif;
      text-anchor: middle;
    }
    
    .storeLegend text {
      fill: white;
      font: 14px sans-serif;
      text-anchor: start;
    }
    
    .d3-container {
      height: 300;
      width: 500;
      position: relative;
    }
    
    .hoverbox {
      border: 2px solid rgb(190,190,190);
      border-radius: 10px;
      background: rgb(255,255,255);
      color: #333;
      display: block;
      font: 14px sans-serif;
      left: 0px;
      padding: 10px;
      position: absolute;
      text-align: left;
      top: 0px;
      z-index: 10;
      opacity: 0;
      box-sizing: border-box;
    }
    
    .hoverbox .username {
      color: #406995;
      font: 14px sans-serif;
      text-anchor: start;
      font-weight: bold;
    }
    
    .hoverbox .reviewTitle {
      color: #888;
      font: 14px sans-serif;
      text-anchor: start;
      margin-top: 5px;
    }
    
    .hoverbox .reviewText {
      color: black;
      font: 12px sans-serif;
      text-anchor: start;
      margin-top: 5px;
      font-style: italic;
    }
    
    



