---
layout: docs
title: Daniel's posts
---

<ul>
  {% for post in site.posts %}

      <span><h1><a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a></h1>
			{{ post.date | date_to_string }}</span>
      {{ post.content }}

  {% endfor %}
</ul>
