---
layout: default
title: Projects
permalink: /projects/
---

## Projects

<ul class="project-list">
{%- assign sorted = site.projects | sort: 'date' | reverse -%}
{%- for project in sorted -%}
  <li>
    <a href="{{ project.url | relative_url }}">{{ project.title }}</a>
    {%- if project.summary -%}
      <p class="summary">{{ project.summary }}</p>
    {%- endif -%}
  </li>
{%- endfor -%}
</ul>


