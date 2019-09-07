---
layout: archive
permalink: /projects/
author_profile: true
title: ""
header:
  image: "/images/binaryblue.jpg"
---

<h2>Projects Listed by Data Science Process<h2>

<h3>1. Import Data<h3>

  <h5>{% for post in site.categories['Import Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>2. Store Data<h3>

  <h5>{% for post in site.categories['Store Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>3. Extract Data<h3>

  <h5>{% for post in site.categories['Extract Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>4. Organize<h3>

  <h5>{% for post in site.categories['Organize Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>5. Tidy<h3>

  <h5>{% for post in site.categories['Tidy Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>6. Transform Data<h3>

  <h5>{% for post in site.categories['Transform Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>7. Visualize Data<h3>

  <h5>{% for post in site.categories['Visualize Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>8. Model Data<h3>

  <h5>{% for post in site.categories['Model Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}


<h3>9. Understand the Data<h3>

  <h5>{% for post in site.categories['Understand the Data'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>10. Communication<h3>

  <h5>{% for post in site.categories['Communication'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
  {% endfor %}

<h3>11. Next Steps<h3>

<h5>{% for post in site.categories['Next Steps'] %}<h5>
 <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
{% endfor %}

<h3>12. Document<h3>

  <h5>{% for post in site.categories['Document'] %}<h5>
   <span><a href="{{ post.url }}">{{ post.title }}</a> &nbsp; {{ post.date | date_to_string }}</span>
   {% endfor %}





<hr>
