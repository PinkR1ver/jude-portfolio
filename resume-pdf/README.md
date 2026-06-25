# Academic Resume PDF Template

This folder contains the LaTeX source for a future academic resume/CV PDF.

It is based on the Awesome CV LaTeX template:

- Upstream: `https://github.com/posquit0/Awesome-CV`
- Template license: CC BY-SA 4.0

## Contents

- `resume.tex`: main entry point.
- `resume/*.tex`: editable resume sections.
- `awesome-cv.cls`: Awesome CV class file.
- `fonts/`: bundled fonts required by the template.

## Build

Use XeLaTeX from this folder:

```bash
cd resume-pdf
xelatex resume.tex
```

Generated files such as `.aux`, `.log`, `.out`, and `.pdf` should not be
committed unless the user explicitly asks for a checked-in PDF artifact.

## Privacy / Content Rule

This imported template has been sanitized to keep only Yichong/Jude Wang's
information. Do not reintroduce third-party CV content from the source template.
