# rileyknoll.com

Riley's personal website — a [Hugo](https://gohugo.io) static site published at
**[rileyknoll.com](https://rileyknoll.com)**.

This repo holds *only* the website: content (markdown), layouts, and CSS. New
posts arrive as pull requests; merging to `main` deploys the site via GitHub
Pages. See [`CLAUDE.md`](CLAUDE.md) for the full picture of how content flows in.

## Run it locally

You need Hugo (the **extended** build). On macOS:

```bash
brew install hugo
```

Then, from the repo root, start the dev server:

```bash
hugo server -D
```

Open **http://localhost:1313** in your browser. The `-D` flag shows draft posts
too. Leave it running — every time you save a file, the page reloads itself
automatically so you can see your change right away.

Press `Ctrl+C` in the terminal to stop the server.

## Make a change and preview it

1. Edit or add a file (most content lives under `content/`).
2. Save it.
3. The browser tab at http://localhost:1313 refreshes on its own — check that it
   looks the way you want.

To add a brand-new post the Hugo way:

```bash
hugo new content/updates/2026-06-24-my-post.md
```

That creates the file from the template in `archetypes/`, ready for you to fill
in. (You can also just copy an existing post in `content/updates/` and rename it.)

## Check the build before pushing

To make sure the site builds cleanly the same way the deploy does:

```bash
hugo
```

This writes the finished site into `public/` (which is git-ignored). If it
finishes with no errors, you're good to push.

## Project layout

```
hugo.toml              site config (title, tagline, base URL)
content/               all the words and posts — edit these
  _index.md            home page
  updates/             dated posts, newest first
  about/               the About page
layouts/               HTML templates (how pages are structured)
static/css/style.css   the stylesheet (colors, fonts, spacing)
```

`content/` is where day-to-day edits happen. `layouts/` and `static/css/` are
the hand-editable theme — change them when you want the site to *look* different.
```
