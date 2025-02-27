---
title: "책 내용 핵심 정리"
layout: archive
permalink: /category/book
---

#### 영어적 사고

{% assign posts = site.posts | where: "categories", "book" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}
