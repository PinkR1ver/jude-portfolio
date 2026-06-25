# Project State

- This repository is the Jekyll source for a research portfolio/resume site.
- The public route is `https://pinkr1ver.github.io/resume/`.
- The current visual direction is the dark portfolio design in this repo, not
  the Digital Garden visual system.
- Visitor-facing collection content remains in `_about/`, `_education/`,
  `_projects/`, and `_publications/`.
- Deployment is indirect: build this repo, then copy generated output into
  `/Volumes/macOSexternal/Documents/proj/pinkr1ver.github.io/resume/`.
- The Digital Garden links out to the portfolio and no longer hosts it as a
  `static-root/resume/` mount.
- Generated `_site/` files are currently noisy in the working tree and should
  not be staged unless the user explicitly asks.
