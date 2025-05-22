---
title: "category : web"
layout: archive
permalink: /category/web
---

#### web

{% assign posts = site.posts | where: "categories", "web" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}

#### web/programming

{% assign posts = site.posts | where: "categories", "web/programming" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}
