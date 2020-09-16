---
layout: defaults/page
permalink: index.html
narrow: true
---

## What I do
<hr />

I'm a versatile software engineer, with skills in Python, C++, Java, JavaScript, and loves exploring and solving computer science problems.

[More about me]({{ site.baseurl}}{% link _pages/about.md %})

## Recent Projects
<hr />

{% for project in site.projects reversed limit:3 %}
{% include components/project-card.html %}
{% endfor %}


## Recent Posts

<hr />

{% for post in site.posts limit:3 %}
{% include components/post-card.html %}
{% endfor %}
