---
layout: default
title: Publications
permalink: /publications/
---

## Publications

<ul class="pub-list">
{%- assign pub_docs = site.publications | sort: 'year' | reverse -%}
{%- for pub in pub_docs -%}
  <li class="pub-item">
    {%- if pub.authors -%}<span class="pub-authors">{{ pub.authors | join: ', ' }}</span>{%- endif -%}
    {%- if pub.year -%} <span class="pub-year">({{ pub.year }})</span>{%- endif -%}
    . <strong>{{ pub.title }}</strong>.
    {%- if pub.venue -%} <em class="pub-venue">{{ pub.venue }}</em>{%- endif -%}
    · <a href="{{ pub.url | relative_url }}">details</a>
  </li>
{%- endfor -%}
</ul>
