---
layout: default
title: homepage
---

<ul style="padding: 0;">
    {% for post in site.posts %}
        {% if post.category contains "chinese" %}
        <li style="list-style: none; font-size: 16px;margin-bottom: 20px;">
            <a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a>
            <span style="float: right;font-size: 14px;">{{ post.date | date_to_string }} </span>
        </li>
        {% endif %}
    {% endfor %}
</ul>