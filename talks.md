---
layout: page
title: Talks
---

<ul>
{% for talk in site.data.talks %}
<li>
    {{ talk.date }} <b>{{ talk.location }}</b>
    <small>{% for link in talk.links %}<a href="{{ link.url }}">{{ link.name }}</a> {% endfor %} </small>
    <br/>{{ talk.title }} <small><b>#{{ talk.tag }}</b></small>
</li>
{% endfor %}
</ul>
