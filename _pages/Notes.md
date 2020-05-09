---
layout: archive
title: "Notes"
permalink: "/notes/"
author_profile: true
---
{% for post in site.notes %}
  {% include notes-list.html %}
{% endfor %}