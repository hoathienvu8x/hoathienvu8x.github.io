---
layout: default
---

<div class="listing">
    <h1>Archives <span>&rsaquo; Recent Posts</span></h1>
    <ol class="listing">
    {% for post in site.posts limit:5 %}
    <li><a href="{{ site.url}}{{ post.url }}">{{ post.title }}</a> <time>{{ post.date | date: "%b %-d, %Y" }}</time></li>
    {% endfor %}
    </ol>
</div>
