---
layout: page
title: Papers
permalink: /papers/
---

{% for paper in site.papers %}
  * [ {{ paper.title }} ]({{ paper.url }})
{% endfor %}
