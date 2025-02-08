---
layout: page
title: About Me
permalink: /about/
comments: false
---

* content
{:toc}


欢迎来到我的博客

这里记录着我的上下求索之路！

<hr>

时光荏苒，岁月蹉跎，一事无成。

什么都没有留下，却还是希望能够留下些什么。

以前还能写些文字，长期不动笔，现在却连句子都不能写的通顺了。

可是终究得写一写，并不求信息汪洋中相遇相知，只希望不惑之年再回首时，少些遗憾。

也许，这就是我挣扎的、平凡的人生...

这里会写一些：

- 阅读笔记
- 旅游感悟
- 有趣的文字
- 有意思的观点
- 简单的解决问题的小技巧
- 简明技术总结

<hr>

## 联系我
<div>
    {% if site.github_username -%}
    <a target="_blank" href="//github.com/{{ site.github_username }}">
        <span class="fa-stack fa-lg">
            <i class="fa fa-circle fa-stack-2x"></i>
            <i class="fa fa-github fa-stack-1x fa-inverse"></i>
        </span>
    </a>
    {%- endif %}
    {% if site.email -%}
    <a target="_blank" href="mailto:{{ site.email }}">
        <span class="fa-stack fa-lg">
            <i class="fa fa-circle fa-stack-2x"></i>
            <i class="fa fa-envelope-o fa-stack-1x fa-inverse"></i>
        </span>
    </a>
    {%- endif %}
    {% if site.rss_feed -%}
    <a target="_blank" href="{{ site.rss_feed | relative_url }}">
        <span class="fa-stack fa-lg">
            <i class="fa fa-circle fa-stack-2x"></i>
            <i class="fa fa-rss fa-stack-1x fa-inverse"></i>
        </span>
    </a>
    {%- endif %}
</div>


<hr>

## 友链
<div>
    {% if site.data.friends -%}
    {% for friend in site.data.friends -%}
    <a href="{{friend.link}}">{{friend.name}}</a><b> | </b>
    {% endfor %}
    <b>...</b>
    {%- endif %}
</div>
