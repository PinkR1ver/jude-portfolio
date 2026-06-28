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
5. Read `resume-pdf/README.md` before changing the academic PDF resume
   template.

## Validation

- Portfolio source validation:
  `bundle exec jekyll build --destination /tmp/jude-portfolio-resume-build`
- If publishing the current snapshot, rebuild here first, then copy
  `/tmp/jude-portfolio-resume-build/.` into the user-site repo's `resume/`
  directory.
- The Digital Garden validation is separate: it should build without a
  `static-root/resume/` mount and link out to `https://pinkr1ver.github.io/resume/`.

## Preview and Publishing Preference

- For visual/UI/content iterations, default to local preview first.
- After implementing and building, start a local preview server and give the user
  the exact preview URL, such as `http://127.0.0.1:<port>/resume/`.
- Keep preview work lightweight: after build/local deploy, provide the URL and
  let the user review by default. Do not spend extra time opening the browser,
  taking screenshots, or doing deeper visual preview checks unless the user asks
  to continue preview QA.
- Do not commit, push, or deploy by default after UI/content changes.
- Only commit/push/deploy when the user explicitly says so, e.g.
  "commit and push", "deploy", "直接上线", or equivalent.

## Coordination Notes

- Do not make this site look like the Digital Garden; keep the current dark
  portfolio visual direction.
- Do not store secrets or private personal data in `.agents/`.
- Update `.agents/docs/deployment.md` when the publishing path changes.
- `resume-pdf/` contains the sanitized Awesome-CV based LaTeX template for
  future academic PDF generation. Keep it free of third-party CV content.
