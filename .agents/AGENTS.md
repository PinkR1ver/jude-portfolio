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

- For visual/UI/content iterations, the user prefers to run and review the
  Jekyll server themselves. After making changes, do not start a preview server
  unless the user explicitly asks for it.
- When the user wants to run a headless remote preview, the preferred shortcut
  is:

  ```bash
  bin/serve-lan
  ```

  This auto-selects a port, binds Jekyll for LAN access, reads `baseurl` from
  `_config.yml`, and prints the laptop-ready Tailscale URL.
- If an agent is explicitly asked to start preview on a headless remote server,
  it must be reachable from the user's laptop over the shared Tailscale network.
  Bind Jekyll to all interfaces, for example:

  ```bash
  bundle exec jekyll serve --host 0.0.0.0 --port <port>
  ```

  Then get the server's Tailscale IPv4 with `tailscale ip -4` and provide the
  laptop URL as `http://<tailscale-ip>:<port>/resume/`. Prefer this direct
  Tailscale URL over SSH local port forwarding.
- By default, finish with validation results and the command the user can run
  for review. Do not open browsers, take screenshots, or perform deeper visual
  preview QA unless the user asks to continue preview QA.
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
