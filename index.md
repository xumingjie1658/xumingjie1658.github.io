---
layout: default
title: 首页
---

<ul style="padding: 0;">
    {% for post in site.posts %}
    <li style="list-style: none; font-size: 16px"><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a><span style="float: right;font-size: 14px;">{{ post.date | date_to_string }} </span></li>
    {% endfor %}
</ul>