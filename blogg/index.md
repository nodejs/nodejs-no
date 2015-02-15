---
layout: left_align
redirect_from: "/blogg.html"
title: Blogg
---

# Blogg

{% for post in site.posts %}
<h2><a href="{{ post.url }}" class="blog-title">{{ post.title }}</a>
{% endfor %}
