---
layout: archive
title: "Notes"
permalink: "/notes/"
author_profile: true
---
{% for post in site.notes %}
  {% include posts-list.html %}
{% endfor %}