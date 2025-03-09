---
layout: default
title: About
permalink: /about/
---

# About me

- Creator and maintainer of [Pixelator](https://mihna123.github.io/pixelator-2.0){:target="_blank"}
- Creator and maintainer of [EESTEC Hub](https://eestec-hub.vercel.app){:target="_blank"}
- Vice-Chairperson for IT @ [EESTEC LC Novi Sad](https://www.eestecns.org){:target="_blank"}
- Studying computer science and the art of BJJ

# Projects

Some of the projects I have been working in the last couple of years. Most of 
these are my personal open source projects and you can check them out on my
[Github page](https://github.com/mihna123){:target="_blank"}

{% for post in site.posts %}
{% if post.categories contains "personal-projects" %}
{% include project-post.html title=post.title url=post.url date=post.date excerpt=post.excerpt %}
{% endif %}
{% endfor %}

# Education

Currently pursuing a degree of **Applied Computer Science**. Right now I am senior
and I am looking to pursue a masters degree in the future.

# Skills

- Node
- React
- NextJS
- MongoDB
- Postgres
- Git
