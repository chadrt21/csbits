---
layout: defaults/page
permalink: index.html
narrow: true
image:
  - Header.jpeg
---

<img src='{{site.url}}/Header.jpeg' width='820'>
<sub>Heater created with [Crello](https://crello.com/home/)</sub>

## Recent Posts

{% for post in site.posts limit:3 %}
{% include components/post-card.html %}
{% endfor %}

## Recent Projects

{% for project in site.projects reversed limit:3 %}
{% include components/project-card.html %}
{% endfor %}
