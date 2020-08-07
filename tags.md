---
layout: page
permalink: /tags/
title: Tags
---

To search just press `CTRL` + `F` and type away a tag name.

Here's a list of tags: {% for tag in site.tags %}{% capture tag_name %}{{ tag | first }}{% endcapture %}<a href="#{{ tag_name }}">{{ tag_name }}</a> {% endfor %}

<div id="archives">
{% for tag in site.tags %}
  <div class="archive-group">
    {% capture tag_name %}{{ tag | first }}{% endcapture %}
    <div id="#{{ tag_name | slugize }}"></div>
    <p></p>

    <h3 class="tag-head">{{ tag_name }}</h3>
    <a name="{{ tag_name | slugize }}"></a>
    {% for post in site.tags[tag_name] %}
    <article class="archive-item">
      <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>

