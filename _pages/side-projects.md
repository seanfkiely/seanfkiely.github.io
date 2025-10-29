---
layout: archive
title: "Side Projects"
permalink: /side-projects/
author_profile: true
---

Here are some of my non-academic projects, including data visualizations, sports analytics, and other explorations that I've created to have fun, learn, and improve my coding skills.

<style>
.project-card {
  width: 300px;
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  background: white;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
}

.project-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 12px rgba(0,0,0,0.2);
}

.project-card img {
  width: 100%;
  height: 180px;
  object-fit: cover;
  transition: opacity 0.3s ease;
}

.project-card:hover img {
  opacity: 0.8;
}

.project-card h3 a {
  color: #333;
  text-decoration: none;
  transition: color 0.2s ease;
}

.project-card:hover h3 a {
  color: #0056b3;
}
</style>

<div class="projects-grid" style="display:flex; flex-wrap:wrap; gap:1.5em; justify-content:center;">
{% for post in site.side_projects %}
  <div class="project-card">
    <a href="{{ post.url | relative_url }}">
      {% if post.thumbnail %}
        <img src="{{ post.thumbnail | relative_url }}" alt="{{ post.title }}">
      {% endif %}
    </a>
    <div style="padding:0.8em;">
      <h3 style="margin:0 0 0.3em 0;">
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h3>
      <p style="font-size:0.9em; color:#444;">{{ post.excerpt }}</p>

      <!-- ✅ Add tag pills INSIDE each card -->
      {% if post.tags %}
        <div style="margin-top:0.5em; display:flex; flex-wrap:wrap; gap:0.4em;">
          {% for t in post.tags %}
            <a href="{{ '/tags/#' | append: t | slugify | relative_url }}"
               style="font-size:0.8em; background:#f0f3f7; border:1px solid #e2e6ea; border-radius:999px; padding:0.15em 0.6em; color:#334; text-decoration:none;">
              #{{ t }}
            </a>
          {% endfor %}
        </div>
      {% endif %}
    </div>
  </div>
{% endfor %}
</div>
