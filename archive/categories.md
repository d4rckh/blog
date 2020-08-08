---
layout: page
permalink: /categories/
title: Categories
---

To search just press `CTRL` + `F` and type away a category or post name.
<h2>Index</h2>
<div id="index">
{% for category in site.categories %}
  <div class="index-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <a class="category-head" href="#{{ category_name }}">{{ category_name }}</a>
  </div>
{% endfor %}
</div>

<div id="archives">
{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>

    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name | slugize }}"></a>
    {% for post in site.categories[category_name] %}
    <article class="archive-item">
      <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>