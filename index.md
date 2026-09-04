---
layout: default
title: Rayan Rathore
---

<div class="nav-container">
  <a href="/">Dashboard</a>
  <a href="/about/">About Me</a>
  <a href="/projects/">Projects</a>
  <a href="/contact/">Contact</a>
</div>

---

## // Security Tools & Write-ups
{% for post in site.posts %}
* **[{{ post.title }}]({{ post.url }})** — *{{ post.date | date: "%B %d, %Y" }}*
{% endfor %}
