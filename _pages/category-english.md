---
title: "영어 학습 내용 정리"
layout: archive
permalink: /category/english
---

#### 영어적 사고

{% assign posts = site.posts | where: "categories", "english/english-thinking" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}
