# Bilingual Content System

- Created: 2026-06-25
- Last updated: 2026-06-25
- Status: Planned

## Goal

Provide English and Chinese versions for all public portfolio pages while keeping
the current English version as the active working surface.

## Scope

- Add bilingual content support for the homepage/about, education, projects,
  publications, and navigation-level labels.
- Preserve the current dark portfolio visual direction.
- Keep the English version as the default until the bilingual routing and
  content structure are deliberately designed.
- Avoid mixing Chinese and English inside the same primary body copy unless a
  specific section benefits from bilingual pairing.

## Initial Direction

- Current work continues on the English version.
- Future implementation should decide between route-based language pages
  (`/resume/` and `/resume/zh/`) or a language toggle backed by structured data.
- Resume PDF localization is out of scope until the web bilingual structure is
  stable.

## Notes

- The English homepage sidebar bio is stored in `_data/profile.yml` as
  `tagline`.
- The English SEO/meta description is stored in `_config.yml` as `description`.
- Keep those concerns separate when adding Chinese content so the visible bio
  can stay short while metadata remains descriptive.
