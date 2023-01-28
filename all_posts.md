---
layout: base
title: All Posts
show_title: true
---

{% for tag in site.tags %}
<h3>{{ tag[0] }}</h3>
<ul class="plain-list">
{% for post in tag[1] %}
<li>
    <a href="{{ post.url }}">{{post.date | date: "%Y-%m-%d"}} {{post.title}}</a>
</li>
{% endfor %}
</ul>
{% endfor %}