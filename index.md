---
layout: home
---

# Instructor Led Training

<div class='chapters'>
{% for p in site.pages %}{% if p.index == 0 %}
  <p><a href="{{site.baseurl | append:p.url}}" title="Start page for: {{p.chapter}}">{{p.chapter}}</a></p>
{% endif %}{% endfor %}
</div>

Code for this site is available from https://github.com/samdutton/ilt.

<!--
{% assign pages = site.pages | sort:"chapter" %}
{% assign chapter = pages.first.chapter %}
{% assign current-chapter = "" %}

{% for p in pages %}{% if p.title %}
{% assign chapter = p.chapter %}
{% if current-chapter != chapter %}
  {% assign current-chapter = chapter %}
  {% assign dir = p.url | split:"/" | pop %}
  <p><a href="{{dir}}" title="Start page for: {{chapter}}">{{chapter}}</a></p>
{% endif %}
{% endif %}{% endfor %}
-->
