---
layout: default
title: Publications
permalink: /publications/
---

## Publications

<ul class="pub-list">
{%- for pub in site.data.publications -%}
  <li>
    <strong>{{ pub.title }}</strong>
    <span class="pub-authors">{{ pub.authors }}</span>
    <span class="pub-venue">{{ pub.venue }}</span>
    <span class="pub-year">{{ pub.year }}</span>
    {%- if pub.url -%} · <a href="{{ pub.url }}" target="_blank" rel="noopener">link</a>{%- endif -%}
  </li>
{%- endfor -%}
</ul>


