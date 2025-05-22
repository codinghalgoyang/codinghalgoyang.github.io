---
title: "category : utils"
layout: archive
permalink: /category/utils
---

#### utils

{% assign posts = site.posts | where: "categories", "utils" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}

#### github-pages

{% assign posts = site.posts | where: "categories", "utils/github-pages" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}
