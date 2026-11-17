# AGENTS.md

Guidance for AI agents (and humans) working in this repository.

## Project Overview

**TestWorx** is a personal technical testing blog by Nick Oppersdorff, covering
Selenium, Playwright, JMeter, test automation, performance testing, and test
design.

- **Type:** Jekyll static site (markdown posts, HTML/CSS/LESS theme)
- **Deployment:** GitHub Pages → `https://blog.testworx.io` (see `CNAME`)
- **Repo:** `testworx/testworx.github.io`, default branch `main` (a legacy `master` branch also exists)
- **Theme:** [Clean Blog](https://github.com/BlackrockDigital/startbootstrap-clean-blog-jekyll) by Start Bootstrap — Bootstrap 3, jQuery, LESS sources. Legacy; a design revamp is in progress.

## Key Commands

```bash
bundle install              # installs into vendor/bundle (see .bundle/config)
bundle exec jekyll serve    # local preview at http://localhost:4000
bundle exec jekyll build    # outputs to _site/ (gitignored)
```

- Ruby 3.3 + Bundler 2.5 on the maintainer's machine. The `github-pages` gem group
  pins Jekyll 3.9.5; if `bundle install` fails on a newer Ruby, that pin is the likely cause.
- `grunt` tasks exist (`Gruntfile.js`) for LESS compile/minify — legacy, rarely needed.
  The checked-in `css/` files are the compiled output.
- There is **no** `.github/workflows` — Pages is configured in the GitHub repo
  settings (source: `main` branch, root). Pushing to `main` deploys.

## Repository Layout

| Path | Purpose |
|---|---|
| `_config.yml` | Site settings: title, description, `url`, social handles, pagination (5/page), gems (`jekyll-paginate`, `jekyll-feed`) |
| `_posts/` | Blog posts (see conventions below) |
| `_layouts/` | `default.html`, `page.html`, `post.html` |
| `_includes/` | `head.html`, `nav.html`, `footer.html`, `disqus.html`, `analytics.html` |
| `css/`, `js/`, `fonts/` | Compiled theme assets (Bootstrap 3, jQuery, Glyphicons) |
| `less/` | LESS sources for the theme (`clean-blog.less`, `variables.less`, `mixins.less`) |
| `img/` | Theme images (`home-bg.jpg`, `post-bg-*.jpg`) **and** some older post images |
| `assets/img/YYYY/month/` | **Current convention** for post images (charts, diagrams, screenshots) |
| `rafts/` | ⚠️ Local-only snapshot of the rendered site (untracked, gitignored) — **do not edit**; safe to delete |
| `mail/contact_me.php` | Contact form handler — requires a PHP host; **does not work on GitHub Pages** |
| `vendor/`, `node_modules/`, `_site/`, `.idea/` | Local/build artifacts, gitignored |

## Content Conventions (Posts)

Follow the pattern of existing posts (e.g. `_posts/2024/december/...`):

1. **File path:** `_posts/YYYY/<lowercase-month>/YYYY-MM-DD-title-slug.markdown`
   (e.g. `_posts/2024/april/2024-04-01-benchmarking-selenium-and-playwright-pt2.markdown`)
2. **Front matter:**
   ```yaml
   ---
   layout:     post
   comments: true
   title:      "Post Title"
   subtitle:   "Optional subtitle"
   date:       2024-04-01 00:00:00
   author:     "Nick Oppersdorff"
   header-img: "img/post-bg-02.jpg"
   ---
   ```
3. **Post images** go in `assets/img/YYYY/<month>/` and are referenced with an
   **absolute** path in markdown: `![alt](/assets/img/2024/march/my-chart.png)`.
   (`permalink: pretty` is set, so URLs are clean — `/2024/03/26/slug/`.)
4. **Diagrams:** Mermaid sources (`.mmd`) are stored next to the post in `_posts/`;
   the rendered PNGs go in `assets/img/` as above.
5. **Multi-part series:** use "Pt. N" in the title and link back to earlier parts
   with absolute URLs (`https://blog.testworx.io/...`).
6. Code is highlighted with **Rouge** (see `css/syntax.css`); markdown is parsed by **kramdown**.

## Gotchas

- **`rafts/` is a local-only build snapshot** (an old copy of the rendered site,
  committed by accident in 2016, now untracked and gitignored). It will disappear
  from the repo — and from the live site at `blog.testworx.io/rafts/` — once the
  untracking is committed and pushed. Note it is **not** in Jekyll's `exclude` list,
  so local builds still copy it into `_site/`. Never edit it; safe to delete locally.
- **`Gemfile.lock`** is no longer gitignored (it was, historically) — it is a normal
  tracked file now. Commit it so the gem set stays reproducible.
- **CNAME / URL mismatch:** `CNAME` is `blog.testworx.io`, but `_config.yml` has
  `url: "http://www.testworx.io"`. The Disqus shortname and in-post links use
  `blog.testworx.io`. Prefer `https://blog.testworx.io` for new links; the config
  value is a known inconsistency.
- **`.DS_Store` files are gitignored** — they won't show up in `git status`.
  Don't commit them.
- **Contact page** (`contact.html`) posts to `mail/contact_me.php`, which GitHub
  Pages will not execute — the form is decorative on the live site.
- **Legacy theme:** Bootstrap 3, jQuery 1.x, IE8 shims, dead `//` protocol-relative
  font links. New design work should not extend this theme; treat it as the baseline
  being replaced.
- Bundler is configured with `BUNDLE_PATH: vendor/bundle` (`.bundle/config`, which is
  tracked) — gems install inside the repo's `vendor/` dir, which is gitignored.

## Verification Checklist (after changes)

1. `bundle exec jekyll build` completes without errors.
2. Preview locally with `bundle exec jekyll serve`; check the post renders with
   correct date, header image, and pagination.
3. Confirm new images are referenced with absolute `/assets/...` paths and exist on disk.
4. For new posts: check the RSS feed (`feed.xml` via `jekyll-feed`) picks the post up.
