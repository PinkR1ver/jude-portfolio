# Coordination Layer Design

**Created:** 2026-06-24  
**Status:** Approved for review

## Goal

Create a lightweight `.agents/` coordination layer for maintaining the Jude
Research Portfolio without moving or duplicating Jekyll-rendered content.

## Scope

The coordination layer will contain:

```text
.agents/
├── AGENTS.md
├── docs/
│   ├── architecture.md
│   ├── content-audit.md
│   └── deployment.md
├── memory/
│   └── project-state.md
└── spec/
    └── README.md
```

It will document the project's actual architecture, current content gaps,
deployment target, durable decisions, and feature-status index.

## Non-goals

- Do not move, duplicate, or rewrite the visitor-facing content in `_about/`,
  `_education/`, `_projects/`, or `_publications/`.
- Do not change visual design, site content, DNS, hosting, `url`, or `baseurl`.
- Do not include generated `_site/` files or Bundler's `vendor/` dependencies
  in coordination documentation.

## Document Responsibilities

### `.agents/AGENTS.md`

The entry point for future agents: stack, local commands, protected content
directories, validation expectations, and when `.agents/` records need updates.

### `.agents/docs/architecture.md`

The source-of-truth map of Jekyll collections, routes, layouts, styles,
scripts, static assets, and data files. It will identify that publications and
education are collection-backed, correcting stale README references to missing
`_data/experience.yml` and `_data/publications.yml`.

### `.agents/docs/content-audit.md`

An indexed audit of the existing About, Education, Project, and Publication
content. It will link to source paths, classify each item's readiness, and list
template residue, sample publications, and missing real-world information as
prioritized work. It will not copy long-form page content.

### `.agents/docs/deployment.md`

The intended public address is `https://pinktalk.online/resume`. This document
will state known configuration gaps and the deployment decisions still required;
it will not prescribe or apply a hosting/DNS change without user direction.

### `.agents/memory/project-state.md`

Durable facts and decisions: this is a research portfolio; its visual language
must remain distinct from the Digital Garden; content collections stay in place;
and the current site build passes locally with a Ruby warning.

### `.agents/spec/README.md`

A compact feature tracker. It will list “Portfolio completion” as planned and
link this design as its current artifact.

## Maintenance Rules

- Write verified facts separately from open questions or planned work.
- Update architecture documentation when routes, collections, layouts, or build
  workflows change.
- Update the content audit when source content is added, replaced, or verified.
- Update deployment documentation only when the target or delivery method
  changes.
- Update project state only for durable decisions, not routine edits.
- Keep the feature-status index synchronized with feature spec state.

## Validation

After creating the coordination documents, run `bundle exec jekyll build` and
confirm that the documents do not affect generated site output. Generated
changes under tracked `_site/` are outside this task and must not be committed.

