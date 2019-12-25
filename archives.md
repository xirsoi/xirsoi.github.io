---
layout: default
title: Archives
permalink: /archives/
---

{% if site.tags.size > 0 %}

## Tags

  {% for arc in site.archives %}

    {% if arc.type == "tag" %}

- [{{ arc.title }}]({{ site.url }}/{{ arc.path }})

    {% endif %}
  {% endfor %}

{%- endif -%}

{% if site.tags.size > 0 %}

## Categories

  {% for arc in site.archives %}

    {% if arc.type == "category" %}

- [{{ arc.title }}]({{ site.url }}/{{ arc.path }})

    {% endif %}
  {% endfor %}

{%- endif -%}
