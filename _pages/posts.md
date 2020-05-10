---
layout: archive
title: "Posts"
permalink: "/posts/"
---
{% for post in site.posts %}
  {% include posts-list.html %}
{% endfor %}