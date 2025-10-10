---
layout: archive
title: "Articles on Programming Languages"
permalink: /cs_pl/
author_profile: true
---

{% include base_path %}

{% for post in site.cs_pl reversed %}
  {% include archive-single.html %}
{% endfor %}