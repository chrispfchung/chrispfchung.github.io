---
layout: archive
permalink: /projects/
title: "Projects"
author_profile: true
header:
  image: "/images/binaryblue.jpg"
---


<div id="main" role="main">
  {% include sidebar.html %}

  <div class="archive">
    <h1 class="page__title">{{ page.title }}</h1>
    {% for post in page.posts %}
      {% include archive-single.html %}
    {% endfor %}
  </div>
</div>
