---
layout: posts
title: "Posts"
permalink: "/posts/"
author_profile: true
---
{% for post in site.posts %}
  {% include posts-list.html %}
{% endfor %}