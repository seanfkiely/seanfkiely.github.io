---
layout: archive
title: "Side Projects"
permalink: /side-projects/
author_profile: true
---

Here are some of my non-academic projects — data visualizations, sports analytics, and other explorations.

{% include base_path %}
<div class="projects-grid" style="display:flex; flex-wrap:wrap; gap:1.5em; justify-content:center;">
{% for post in site.side_projects %}
  <div class="project-card" style="width:300px; border:1px solid #ddd; border-radius:8px; overflow:hidden; box-shadow:0 2px 4px rgba(0,0,0,0.1); background:white;">
    <a href="{{ post.url | relative_url }}">
      {% if post.thumbnail %}
        <img src="{{ post.thumbnail | relative_url }}" alt="{{ post.title }}" style="width:100%; height:180px; object-fit:cover;">
      {% endif %}
    </a>
    <div style="padding:0.8em;">
      <h3 style="margin:0 0 0.3em 0;"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      <p style="font-size:0.9em; color:#444;">{{ post.excerpt }}</p>
    </div>
  </div>
{% endfor %}
</div>

