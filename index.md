---
layout: page
title: Posts
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date: '%Y/%m/%d' }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

<br/>
<h3>Products</h3>
<hr/>
<div class="row">
  <div class="span5 offset1">
    <div class="well">
      <h3><a href="http://www.dosancole.com/dsclgrid">dsclgrid.js</a></h3>
    </div>
  </div>
  <div class="span5">
    <div class="well">
      <h3><a href="http://www.dosancole.com/dscltable">dscltable.js</a></h3>
    </div>
  </div>
</div>


