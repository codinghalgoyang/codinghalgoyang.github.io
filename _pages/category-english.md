---
title: "영어 학습 내용 정리"
layout: archive
permalink: /category/english
---

#### 왕초보영어

{% assign posts = site.posts | where: "categories", "english/english-beginner" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}


#### 영어적 사고

{% assign posts = site.posts | where: "categories", "english/english-thinking" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}
