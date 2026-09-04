---
layout: default
title: Dashboard
---

### [Dashboard](index.html) &bull; [About Me](about.html) &bull; [Projects](projects.html) &bull; [Contact](contact.html)

---

## $ whoami
I am a developer documenting my progress at the intersection of **Python, Machine Learning, and Cloud Security Infrastructure**. This space tracks my code tools, deep-dives, and hands-on projects.

---

## // Security Tools & Write-ups
{% for post in site.posts %}
* **[{{ post.title }}]({{ post.url }})** — *{{ post.date | date: "%B %d, %Y" }}*
{% endfor %}
