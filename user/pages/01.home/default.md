---
title: Gavin Kimpson
---

# Gavin Kimpson

Software Engineer

I build web applications with PHP, JavaScript, and modern frameworks. Arsenal supporter. Proud dad to two amazing children. #COYG

## Latest posts

{% set posts = page.find('/blog').children.order('date', 'desc').limit(5) %}

{% for post in posts %}
* [{{ post.title }}]({{ post.url }}) — {{ post.date|date('F j, Y') }}
{% endfor %}

[View all posts →](/blog)
