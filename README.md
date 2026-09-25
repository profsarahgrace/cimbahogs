# CimbaHogs (cimbahogs.com)

A public information site for University of Arkansas students and families considering
CIMBA Italy study abroad. It is a [Jekyll](https://jekyllrb.com/) site hosted on
GitHub Pages, deployed straight from the `main` branch.

Live pages:

| URL | Source file |
|---|---|
| `cimbahogs.com/` | `index.html` |
| `cimbahogs.com/classfinder/` | `classfinder/index.html` |
| `cimbahogs.com/stories/` | `stories/index.html` |
| `cimbahogs.com/summer27application/` | `summer27application/index.html` |

## How the site is put together

Every page is **front matter + body content only**. The parts every page shares
(the `<head>`, meta/social tags, JSON-LD, fonts, and the navbar) live in one place each:

```
_config.yml                  site-wide settings (URL, default social image, shared JSON-LD entities)
_layouts/default.html        the page shell: <head>, meta/OG/Twitter tags, fonts, nav, then your content
_includes/nav.html           the navbar and its menu-toggle script
_includes/jsonld.html        the JSON-LD block, built from each page's front matter
assets/css/nav.css           navbar styles shared by every page
assets/css/home.css          styles for the home page
classfinder/classfinder.css                   styles for the class finder
summer27application/summer27application.css  styles for the application guide
images/                      photos (the social-sharing image is images/scrap-modern-1.jpg)
videos/                      web-ready videos (H.264 MP4). Keep them small; never commit the original camera file
CNAME                        tells GitHub Pages the custom domain. Do not delete or edit.
sitemap.xml, robots.txt, llms.txt   hand-maintained (see "Keep these in sync")
```

Pages keep the folder-per-page layout (`name/index.html`) so URLs end in a slash,
like `/classfinder/`.

## Everyday edits

**Change a nav link, or the nav's look:** edit `_includes/nav.html` (links) or
`assets/css/nav.css` (styling). Every page updates.

**Change a page's title, description, or social-sharing text:** edit the front matter
(the block between the `---` lines at the very top of that page's `index.html`).

**Change a page's content:** edit below the front matter. Write only the page body: no
`<html>`, `<head>`, `<body>`, or navbar. The layout adds those.

**Change a page's styling:** edit that page's own CSS file (listed above). Navbar
styling belongs in `nav.css`, not in a page's CSS.

### Front matter reference

```yaml
---
layout: "default"                       # always this
title: "Page title | CimbaHogs"         # <title>
description: "Meta description."
css: "/folder/page.css"                 # the page's own stylesheet
seo: true                               # turns on canonical, Open Graph, Twitter, JSON-LD
og_type: "website"                      # or "article"
og_title: "..."                         # optional; defaults to title
og_description: "..."                   # optional; defaults to description
twitter_title: "..."                    # optional; defaults to og_title, then title
twitter_description: "..."              # optional
schema_name: "..."                      # JSON-LD name (defaults to title)
schema_description: "..."               # JSON-LD description (defaults to description)
date_modified: "2026-09-23"             # JSON-LD dateModified: update by hand, keep the quotes
keywords: ["...", "..."]                # JSON-LD keywords
audiences: ["..."]                      # JSON-LD audience
about_extra: [...]                      # optional extra JSON-LD "about" entities
video:                                  # optional; adds VideoObject JSON-LD (see stories/index.html)
  file: "/videos/name.mp4"
  poster: "/images/poster.jpg"
  name: "..."
  description: "..."
  upload_date: "2026-09-24"
  duration: "PT1M4S"                    # ISO 8601 duration
  width: 540
  height: 960
viewport: "..."                         # optional; overrides the default viewport tag
fonts: "https://fonts.googleapis.com/..." # optional; overrides the default Google Fonts URL
---
```

Always wrap text values in double quotes. A stray colon or `@` in unquoted YAML breaks
the build.

## Adding a new page

1. Create `newpage/index.html` with front matter (copy one from an existing page) and
   your body content. Add `newpage/newpage.css` if it needs its own styles.
2. Add a link in `_includes/nav.html`. If it should show as the current page, copy the
   `{% if page.url == '...' %} aria-current="page"{% endif %}` pattern used by the other links.
3. Add it to `sitemap.xml` and `llms.txt`.

## Publishing changes safely

GitHub Pages only builds `main`, so a change is live about a minute after it lands
there. To test first:

1. Work on a branch (`git checkout -b my-change`) and push it.
2. Pages does **not** build other branches. To prove a branch builds, temporarily add a
   workflow that runs GitHub's own Pages builder without deploying. The one used for this
   site's conversion is in the git history:
   ```bash
   git show 72001db:.github/workflows/jekyll-test-build.yml
   ```
   Save it as `.github/workflows/jekyll-test-build.yml`, change `branches: [jekyll-test]`
   to your branch name, and push. Check the run in the repo's **Actions** tab (or
   `gh run list`). **Delete the workflow file before merging** so it doesn't land on `main`.
3. Merge to `main` with a merge commit (`git merge --no-ff my-change`), so the whole
   change can be undone in one step:
   ```bash
   git revert -m 1 <merge-commit> && git push origin main
   ```
4. After the deploy finishes, spot-check the live URLs. Browsers cache CSS for a few
   minutes, so hard-refresh (Cmd+Shift+R).

There is no local preview: the Ruby that ships with macOS is too old to run Jekyll.
The test build on GitHub is the check.

## Adding a video

Don't commit the original file: camera and phone exports are tens of MB and stay in git
history forever. Re-encode to H.264 MP4 first (roughly 1 Mbps for a 540x960 vertical
video works well, about 9 MB per minute), put it in `videos/`, and add a poster image in
`images/`. See `stories/index.html` for the markup (`preload="none"` keeps the page fast).
Get the creator's permission and confirm any music is cleared for web use.

## Gotchas

- **Don't paste a complete HTML page into a page file.** Files with their own `<head>`
  and navbar would get a second one from the layout. Convert them to front matter + body
  first.
- **`{{` and `{%` are template syntax.** If page content (including inline JavaScript)
  ever needs those characters, wrap that section in `{% raw %}` ... `{% endraw %}`.
  No page uses them today.
- **Keep these in sync by hand:** `sitemap.xml` (page list and `lastmod` dates),
  `llms.txt` (page list), and each page's `date_modified`.
- **Don't edit `CNAME`.** Changing it breaks the custom domain.
- **This README is not published.** `_config.yml` excludes it from the built site.

## Tools

Git, plus the GitHub CLI (`gh`, installed at `~/bin/gh` and on the PATH). `gh auth status`
shows whether you're signed in; `gh auth login -s workflow` signs in with permission to
push workflow files.
