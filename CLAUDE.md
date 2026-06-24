# Project context: rileyknoll.com — Riley's personal website

## What this repo is
A **public, content-only Hugo static site** for Riley, published at **rileyknoll.com**.
It is one of N per-kid site repos managed by a **separate, private central agent**
(`AI_site_builder`). This repo holds *only* the website: Hugo markdown content,
layouts, and CSS. **No application code, no secrets, no email addresses.**

How content gets here: a kid emails the site's intake address → the central agent
(in its own repo) calls the Anthropic API, validates the result through Pydantic,
and opens a **PR against this repo** that touches only `content/`. Merging the PR
deploys the site. Parent review of that PR *is* the moderation gate.

This repo is **public** because it's a public website anyway and carries no PII.
GitHub Pages on the free plan only publishes public repos — which is fine here.
(The agent repo that knows Riley's email/allowed-senders is the private one.)

## Hard boundary: who edits what
- **The agent writes `content/` only — markdown, never HTML.** A narrow output
  schema is what keeps a cheap model reliable. This is enforced by **branch-protection
  path filters**, not model judgment. Do not widen the agent's reach into `layouts/`
  or `static/`.
- **`layouts/` and `static/css/` are human-owned, deliberately.** Riley is learning
  HTML/CSS; editing the real layout/style by hand is the *feature*, not a gap.
  Theming is intentionally NOT exposed to the agent. When you work in here, **favor
  legible, teachable layouts and CSS over clever code** — a kid should be able to
  read and edit it.

## Layout
```
hugo.toml              # site config: title, baseURL, [params] tagline
archetypes/default.md  # frontmatter template for `hugo new`
content/
  _index.md            # home page body
  updates/             # the dated post feed (newest first)
    _index.md          # section landing page
    YYYY-MM-DD-slug.md # one post per file
  about/_index.md      # editable About page
  <topic>/             # topic sections (e.g. dinosaurs/) — created on demand
    _index.md          # section landing page
    YYYY-MM-DD-slug.md # dated posts filed under the topic
layouts/
  _default/{baseof,list,single}.html
  index.html
  partials/nav.html    # auto-generates nav: Home + one link per top-level section
static/css/style.css   # the whole stylesheet; CSS custom props at :root
.github/workflows/deploy.yml  # builds Hugo + deploys to GitHub Pages on push
```

## Content model (what the agent produces — three operations)
- **`add_update`** — a dated post. Default lands in `content/updates/`. Has an
  optional `section`: a clearly-topical post routes into a *matching existing*
  section (e.g. a T-rex post → `dinosaurs/`); when in doubt it stays in Updates.
  If a requested section dir doesn't exist, it falls back to `updates/` rather
  than spawning a stray section.
- **`create_section`** — a new topic area (a new `content/<slug>/_index.md`).
  New sections come *only* from this explicit op. Once a section exists, later
  posts can route into it.
- **`edit_page`** — rewrite/append a section's `_index.md` landing page (e.g. About).

## Conventions
- **Frontmatter is TOML** (`+++ ... +++`), not YAML.
- **Dated posts:** filename `YYYY-MM-DD-kebab-slug.md`; frontmatter `title`, `date`
  (quoted `YYYY-MM-DD`), optional `tags = [...]`.
- **Nav is automatic.** `partials/nav.html` ranges `.Site.Sections`, so a new
  top-level section appears in the nav with no template change. `list.html`
  ranges `.Pages`, so any section with child posts renders a clickable dated list.
  You rarely need to touch templates to add content areas.
- **Newest-first feeds** come from Hugo's default date ordering — keep dates accurate.

## Photos (served from Cloudflare R2, NOT committed to git)
Phone originals never land in this repo. The agent's pipeline resizes to WebP
(max 1600px, q80, EXIF auto-rotate, **GPS stripped**) and uploads to a public R2
bucket exposed at **`img.rileyknoll.com`**. Posts embed a plain markdown image
pointing at that URL: `![](https://img.rileyknoll.com/<site>/<date>-issue-<N>/photo-<n>.webp)`.
Standard markdown `<img>` renders fine in Hugo — no page bundles. **Do not commit
image binaries here**; if you're adding a photo by hand, put it on R2 and link it.

## Deploy
- **GitHub Pages**, built by `.github/workflows/deploy.yml`. **Merge to the default
  branch = deploy.** One-time setup: Settings → Pages → Source = "GitHub Actions".
- Custom domain `rileyknoll.com` via Cloudflare DNS (CNAME → Pages), Let's Encrypt
  cert, HTTPS enforced.
- Local preview: `hugo server -D` (drafts visible). Build check: `hugo`.

## Working notes for Claude Code in this repo
- Default to editing/creating files under `content/`. Touching `layouts/` or CSS
  is a human/teaching task — flag it rather than doing it silently as part of a
  content change.
- Match the existing markdown/frontmatter style of neighboring files exactly.
- Git history here is a deliberate **time capsule** of what Riley chose to publish —
  prefer additive commits with clear messages; don't rewrite history.
- This is a kid's site: keep copy warm and simple, emoji are welcome, and assume
  every change is visible to a child who may one day read this repo.
