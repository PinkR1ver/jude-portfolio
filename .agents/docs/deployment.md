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
