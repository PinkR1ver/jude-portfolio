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
    <p class="project-meta">
      {%- if project.duration -%}<span>{{ project.duration }}</span>{%- endif -%}
      {%- if project.tags -%}<span> · {{ project.tags | join: ', ' }}</span>{%- endif -%}
    </p>
  </li>
{%- endfor -%}
</ul>


