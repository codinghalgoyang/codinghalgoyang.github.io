---
title: "category : web"
layout: archive
permalink: /category/web
---

#### web

{% assign posts = site.posts | where: "categories", "web" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}

#### web/database

{% assign posts = site.posts | where: "categories", "web/database" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}

#### web/html

{% assign posts = site.posts | where: "categories", "web/html" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}

#### web/css

{% assign posts = site.posts | where: "categories", "web/css" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}

#### web/typescript

{% assign posts = site.posts | where: "categories", "web/typescript" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}

#### web/react

{% assign posts = site.posts | where: "categories", "web/react" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}

#### web/nextjs

{% assign posts = site.posts | where: "categories", "web/nextjs" %}
{% for post in posts %} {% include archive-single-custom.html type=page.entries_layout %} {% endfor %}
