---
layout: left_align
title: Blogg
---

# Blogg

{% for post in site.posts %}
<h2><a href="{{ post.url }}" class="blog-title">{{ post.title }}</a>
{% endfor %}
