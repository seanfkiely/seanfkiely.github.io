---
layout: archive
title: "Sean Kiely"
permalink: /
author_profile: true
redirect_from:
  - /about/
  - /about.html
---

{% include base_path %}
{% capture written_year %}'None'{% endcapture %}
{% for post in site.research reversed %}
  {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
  {% if year != written_year %}
    <h2 id="{{ year | slugify }}" class="archive__subtitle">{{ year }}</h2>
    {% capture written_year %}{{ year }}{% endcapture %}
  {% endif %}
  {% include archive-single.html %}
{% endfor %}

Welcome! I am a Postdoctoral Fellow at **[The National Bureau of Economic Research](https://www.nber.org/)**.
I received my Ph.D. in the **[ARE Department](https://are.ucdavis.edu/)** at the University of California, Davis.  My fields of interest are agricultural, behavioral, and development economics. I am currently studying questions on labeling information, experience effects and motivated beliefs, risk and subjective beliefs, and disability policy in developing countries.

Education 🎓
=========
PhD. in Agricultural and Resource Economics (2024),
<!--Dissertation: “”
Committee: Kristin Kiesel, Travis Lybbert, Anujit Chakraborty-->
University of California, Davis

MSc. in Agricultural and Resource Economics (2019),
University of California, Davis

MSc. in International Agricultural Development (2019),
University of California, Davis

BSc. in Community, Environment, and Development (2013),
Pennsylvania State University

BSc. in Environmental Resource Management (2013),
Pennsylvania State University

Job Market Candidate
====================
Please feel free to contact me with any inquiries at [kielys[at]nber.org](mailto:kielys@nber.org)

