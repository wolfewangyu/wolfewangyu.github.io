---
layout: default
title: 首页
---
王主任办公室

## 最新博客文章

{% for post in site.posts %}
  <article>
    <h2>{{ post.title }}</h2>
    <div>{{ post.content }}</div>
  </article>
{% endfor %}
