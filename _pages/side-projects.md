---
layout: archive
title: "Side Projects"
permalink: /side-projects/
author_profile: true
---

Here's where I will be collecting some of my non-academic projects — data visualizations, sports analytics, and other explorations I’ve done for fun and to improve my coding skills.

{% include base_path %}

{% for post in site.side_projects %}
  {% include archive-single.html %}
{% endfor %}
