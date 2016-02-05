---
title: User Guide
layout: website-normal
breadcrumbs:
- /website/documentation/index.md
- index.md
---

This is the Brooklyn User Guide for v{{ site.brooklyn-version }}:

{% include list-children.html %}

If you are working with the Brooklyn codebase itself, also see the [Developer Guide](dev/) for this version.

{% for product in site.pages %} {% capture length %}{{ forloop.length }}{% endcapture %} {% endfor %}

PAGES = {{ length }}

{% for node in site.pages  %}
* {{ node.dir }}: {{ node.title }} 
{% endfor %}




