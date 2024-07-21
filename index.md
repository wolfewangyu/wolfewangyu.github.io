---
layout: default
title: 首页
---

# 欢迎来到我的博客

这是我的个人博客。在这里你可以找到我的最新文章和更新。

## 最新博客文章

{% for post in site.posts %}
  <article>
    <h2>{{ post.title }}</h2>
    <div>{{ post.content }}</div>
  </article>
{% endfor %}
