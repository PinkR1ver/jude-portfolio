# Agent Entry Point

This repository is the Jekyll source for Jude Wang's research portfolio/resume
site.

## Current Deployment Shape

- Canonical public route: `https://pinkr1ver.github.io/resume/`
- Jekyll config uses `url: "https://pinkr1ver.github.io"` and
  `baseurl: "/resume"`.
- The site is published as static files into the GitHub user-site repository at:
  `/Volumes/macOSexternal/Documents/proj/pinkr1ver.github.io/resume/`
- The Digital Garden should link to this route instead of hosting the portfolio
  under `pinktalk.online`.

## Before Editing

1. Run `git status --short --branch`.
2. Preserve existing dirty generated files under `_site/`; they are build-output
   noise unless the user explicitly asks to update tracked generated output.
3. Keep visitor-facing collection content in place:
   `_about/`, `_education/`, `_projects/`, `_publications/`.
4. Read `.agents/docs/deployment.md` before changing `baseurl`, `url`, deploy
   workflow assumptions, or generated static artifacts.

## Validation

- Portfolio source validation:
  `bundle exec jekyll build --destination /tmp/jude-portfolio-resume-build`
- If publishing the current snapshot, rebuild here first, then copy
  `/tmp/jude-portfolio-resume-build/.` into the user-site repo's `resume/`
  directory.
- The Digital Garden validation is separate: it should build without a
  `static-root/resume/` mount and link out to `https://pinkr1ver.github.io/resume/`.

## Coordination Notes

- Do not make this site look like the Digital Garden; keep the current dark
  portfolio visual direction.
- Do not store secrets or private personal data in `.agents/`.
- Update `.agents/docs/deployment.md` when the publishing path changes.
