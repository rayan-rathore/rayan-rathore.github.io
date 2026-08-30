---
layout: default
title: Home
---

# Welcome to My Python Journey

This is my personal domain where I document my code, my mistakes, and my progress transitioning from a beginner to a developer.

## Articles
{% for post in site.posts %}
* **[{{ post.title }}]({{ post.url }})** — {{ post.date | date: "%B %d, %Y" }}
{% endfor %}
