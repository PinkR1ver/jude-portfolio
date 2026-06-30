# Deployment Notes

## Public Target

The portfolio is intended to live at:

```text
https://pinkr1ver.github.io/resume/
```

The site is configured for that subpath in `_config.yml`:

```yaml
url: "https://pinkr1ver.github.io"
baseurl: "/resume"
```

Internal links and assets should use Jekyll's `relative_url` filter or relative
asset URLs so they remain valid under `/resume/`.

## Publishing Path

This repository is the source of the portfolio, but the live deployment snapshot
is published through the GitHub user-site repository:

```text
/Volumes/macOSexternal/Documents/proj/pinkr1ver.github.io/
```

Current flow:

1. Build this Jekyll site:

   ```bash
   bundle exec jekyll build --destination /tmp/jude-portfolio-resume-build
   ```

2. Copy the generated output into the user-site repository's `/resume/` folder:

   ```bash
   rm -rf /Volumes/macOSexternal/Documents/proj/pinkr1ver.github.io/resume
   mkdir -p /Volumes/macOSexternal/Documents/proj/pinkr1ver.github.io/resume
   cp -R /tmp/jude-portfolio-resume-build/. /Volumes/macOSexternal/Documents/proj/pinkr1ver.github.io/resume/
   ```

3. Commit and push the user-site repository on branch `master`. GitHub Pages
   serves `resume/index.html` as `https://pinkr1ver.github.io/resume/`.

## Related Digital Garden State

As of 2026-06-25:

- User-site repo: `PinkR1ver/pinkr1ver.github.io`
- User-site deploy branch: `master`
- Old Quartz resume note was moved from `content/resume.md` to
  `content/arch/resume.md`.
- `arch` is ignored by Quartz so the old resume no longer renders as
  `/resume.html`.
- The Digital Garden now links out to `https://pinkr1ver.github.io/resume/`
  instead of hosting portfolio files under `pinktalk.online/resume/`.

## Verification Checklist

Run from this repository:

```bash
bundle exec jekyll build --destination /tmp/jude-portfolio-resume-build
```

Run from the user-site repository after copying output:

```bash
test -f resume/index.html
rg "https://pinkr1ver.github.io/resume" resume/index.html resume/sitemap.xml
```

Known warning: the Jekyll build currently emits a Ruby 3.5 `logger` deprecation
warning through vendored Jekyll dependencies. The build still exits 0.

## Remote Preview over Tailscale

This repo is often served from a headless remote machine while the user opens
the preview from a laptop on the same Tailscale network. In that workflow,
prefer direct Tailscale access over SSH local port forwarding.

The user normally runs and reviews the preview themselves. Agents should not
start Jekyll preview servers after routine changes unless explicitly asked; give
the command below instead.

Preferred shortcut:

```bash
bin/serve-lan
```

The shortcut auto-selects an available port, binds Jekyll to `0.0.0.0`, reads
`baseurl` from `_config.yml`, and prints the Tailscale URL for the laptop.

Manual equivalent:

1. Choose an available preview port, commonly `4000` or a nearby free port.
2. Start Jekyll bound to all interfaces:

   ```bash
   bundle exec jekyll serve --host 0.0.0.0 --port <port>
   ```

3. Get the remote machine's Tailscale IPv4 address:

   ```bash
   tailscale ip -4
   ```

   If that command is unavailable, inspect the Tailscale interface:

   ```bash
   ip addr show tailscale0
   ```

4. Tell the user to open this from their laptop:

   ```text
   http://<tailscale-ip>:<port>/resume/
   ```

When validating from the remote server, check both loopback and the Tailscale
address when possible:

```bash
curl -I http://127.0.0.1:<port>/resume/
curl -I http://<tailscale-ip>:<port>/resume/
```
