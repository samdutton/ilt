---
layout: home
---

# Instructor Led Training

{% assign pages = site.pages | sort:"chapter" %}
{% assign chapter = pages.first.chapter %}

{% assign current-chapter = "" %}

<div class='chapters'>
{% for p in pages %}{% if p.title %}
{% assign chapter = p.chapter %}
{% if current-chapter != chapter %}
  {% assign current-chapter = chapter %}
  {% assign dir = p.url | split:"/" | pop %}
  <p><a href="{{dir}}" title="Start page for: {{chapter}}">{{chapter}}</a></p>
  {% endif %}
{% endif %}{% endfor %}
</div>
