---
layout: default
title: Home
---

<section id="about" class="measure" data-template="about">
  {%- assign about_docs = site.about -%}
  {%- if about_docs == nil or about_docs == empty -%}
  {%- assign about_docs = site.collections['about'].docs -%}
  {%- endif -%}
  {%- if about_docs and about_docs != empty -%}
  {%- assign about_docs = about_docs | sort: 'order' -%}
  {%- for a in about_docs -%}
    {{ a.content | markdownify }}
  {%- endfor -%}
  {%- else -%}
  <p class="lead text-90">Write your about content in <code>_about/*.md</code>.</p>
  {%- endif -%}
</section>

<section id="education" class="reveal" data-template="experience">
  <h2 class="eyebrow">Education</h2>
  <ul class="timeline">
  {%- assign edu_docs = site.education -%}
  {%- if edu_docs == nil or edu_docs == empty -%}
  {%- assign edu_docs = site.collections['education'].docs -%}
  {%- endif -%}
  {%- if edu_docs and edu_docs != empty -%}
  {%- assign edu_docs = edu_docs | sort: 'period' | reverse -%}
  {%- for edu in edu_docs -%}
    <li>
      <div class="xp-hover-bg" aria-hidden="true"></div>
      <div class="xp-period">{{ edu.period }}</div>
      <div class="xp-body">
        <div class="xp-title">
          <a href="{{ edu.url | relative_url }}" class="xp-link">
            <strong>{{ edu.degree }}</strong>
            <span class="company"> · {{ edu.school }}</span>
          </a>
        </div>
        {%- if edu.summary -%}
        <p class="xp-summary">{{ edu.summary }}</p>
        {%- endif -%}
        {%- if edu.stack -%}
        <ul class="xp-tags">
          {%- for tag in edu.stack -%}
          <li><span class="pill">{{ tag }}</span></li>
          {%- endfor -%}
        </ul>
        {%- endif -%}
      </div>
    </li>
  {%- endfor -%}
  {%- endif -%}
  </ul>
</section>

<section id="projects" class="reveal" data-template="projects">
  <h2 class="eyebrow">Projects</h2>

  <ul class="project-list">
  {%- assign featured = site.projects | sort: 'date' | reverse | slice: 0, 9 -%}
  {%- for project in featured -%}
    <li class="reveal">
      <a href="{{ project.url | relative_url }}">{{ project.title }}</a>
      {%- if project.summary -%}
        <p class="summary">{{ project.summary }}</p>
      {%- endif -%}
    </li>
  {%- endfor -%}
  </ul>
</section>

<section id="publications" class="reveal" data-template="publications">
  <h2 class="eyebrow">Publications</h2>

  <ul class="pub-list">
  {%- assign pub_docs = site.publications -%}
  {%- if pub_docs == nil or pub_docs == empty -%}
  {%- assign pub_docs = site.collections['publications'].docs -%}
  {%- endif -%}
  {%- if pub_docs and pub_docs != empty -%}
    {%- assign pub_docs = pub_docs | sort: 'year' | reverse -%}
    {%- for p in pub_docs limit: 9 -%}
    <li class="reveal pub-item">
      {%- if p.authors -%}
        <span class="pub-authors">{{ p.authors | join: ', ' }}</span>
      {%- endif -%}
      {%- if p.year -%}
        <span class="pub-year">({{ p.year }})</span>
      {%- endif -%}
      . <span class="pub-title"><strong>{{ p.title }}</strong></span>.
      {%- if p.venue -%} <em class="pub-venue">{{ p.venue }}</em>{%- endif -%}
      {%- if p.url -%} <a href="{{ p.url }}" target="_blank" rel="noopener" class="pub-link">link</a>{%- endif -%}
    </li>
    {%- endfor -%}
  {%- endif -%}
  </ul>
</section>
