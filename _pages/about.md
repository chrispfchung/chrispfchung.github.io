---
title: "About"
permalink: /about/
author_profile: true
header:
  image: "/images/slanted-world.jpg"
---

While I explored different fields of interest, I noticed that most members on a team of venture builders had experience with data. I was intrigued and wanted to learn to work with data to help companies achieve their goals. As a data scientist, I also share a passion for using data to make positive business impacts. I have experience in data acquisition, data modeling, statistical analysis and machine learning. With a background in data analysis, retail and client facing, I want to help gaming companies increase their top line.


{% include base_path %}
{% include group-by-array collection=site.posts field="" %}

{% for tag in group_names %}
  {% assign posts = group_items[forloop.index0] %}
  <h2 id="{{ tag | slugify }}" class="archive__subtitle">{{ tag }}</h2>
  {% for post in posts %}
    {% include archive-single.html %}
  {% endfor %}
{% endfor %}
