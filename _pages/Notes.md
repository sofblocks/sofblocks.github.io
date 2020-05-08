---
layout: archive
title: "Notes"
permalink: "/notes/"
author_profile: true
---
{% for post in site.posts %}
  {% include posts-list.html %}
{% endfor %}