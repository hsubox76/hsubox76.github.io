---
author: Christina Holland
comments: true
date: 2015-05-19 00:24:30+00:00
layout: page
link: http://www.christinahsuholland.com/blog/
slug: blog
title: Blog
---
<h1>Blog Archive</h1>
<div>
  {% for post in site.posts %}
    <div class="post-excerpt-box">
      <div class="post-excerpt-header">
        <a class="post-title" href="{{ post.url }}">{{ post.title }}</a>
        <div class="post-date">{{ post.date | date_to_string }}</div>
      </div>
      <div class="post-excerpt">
        {{ post.excerpt }}
        <div class="category-tag-container">
          {% for category in post.categories %}
          <span class="post-category-tag">{{ category }}</span>
          {% endfor %}
        </div>
      </div>
    </div>
  {% endfor %}
</div>