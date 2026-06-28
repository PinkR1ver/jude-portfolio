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
- `resume-pdf/` stores a sanitized Awesome-CV based LaTeX template for future
  academic resume PDF generation. It was imported from the local reference
  academic-CV template folder but stripped down to Yichong/Jude Wang's
  information only; do not reintroduce third-party CV content.
- The web page exposes the generated academic CV PDF at
  `assets/pdf/yichong-wang-cv.pdf`; regenerate it from `resume-pdf/resume.tex`
  when PDF content changes.
- Education detail pages render transcript PDFs in the main content section,
  not in sidebar Links. Transcript PDFs are stored in this repo under
  `assets/pdf/transcripts/`; the source copies were imported from the Digital
  Garden repo at
  `/Volumes/macOSexternal/Documents/proj/my-digital-garden/content/assets/pdf/`.
  Keep transcript URLs baseurl-safe with `relative_url` because the site is
  served under `/resume/`.
- The Awesome-CV `\cvsection` macro is patched to start with `\par`; keep this
  guard because otherwise a section following plain text can render inline with
  the previous paragraph.
- The current web avatar is `assets/img/avatar/jude-camera-grid.jpg`, optimized
  from the user-provided camera portrait.
- The homepage uses lightweight CSS/JS fluid motion only; preserve
  `prefers-reduced-motion` behavior for accessibility.
- The About copy is intentionally written as an AI-native project portrait,
  distilled from the user's project folders, prototypes, notes, and agent traces
  rather than as a generic academic or job-search bio. The provenance note should
  read as a quiet corner signature, not as a prominent sci-fi card.
