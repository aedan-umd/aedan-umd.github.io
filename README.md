# aedan-umd.github.io

Personal homepage, built with the [UMD Design System](https://designsystem.umd.edu).

Served from two places, same source:

- **Primary:** https://aedan-umd.github.io/ (GitHub Pages, auto-deployed on push)
- **Mirror:** https://www.terpconnect.umd.edu/~aedan/ (rsync from `dist/`)

GitHub Pages is canonical for now (see the `<link rel="canonical">` in
`index.html` — swap that one line to flip it to TerpConnect later).

The git repo is the source of truth; only the built `dist/` output ever
reaches TerpConnect's `pub` folder, so `.git` is never exposed publicly.

## Stack

- [Vite](https://vitejs.dev) — dev server + bundler
- UMD Design System packages:
  - `@universityofmaryland/web-token-library` — brand tokens (colors, type, spacing)
  - `@universityofmaryland/web-styles-library` — utility classes + base styles
  - `@universityofmaryland/web-elements-library` — element builders (peer dep)
  - `@universityofmaryland/web-components-library` — `<umd-element-*>` Web Components

No university header is used — that's reserved for official UMD unit sites.
This is a personal student page, so it uses the system's tokens, styles, and
components only.

## Develop

```bash
npm install
npm run dev        # local dev server with hot reload
```

## Build

```bash
npm run build      # outputs static site to dist/
npm run preview    # serve the built site locally to check it
```

`vite.config.js` sets `base: './'` so all asset paths are relative. This works
for both the GitHub Pages root (`/`) and the TerpConnect subpath (`/~aedan/`).

## Deploy

### GitHub Pages (primary — automatic)

Push to `main` and the workflow in `.github/workflows/deploy.yml` builds with
Vite and publishes to Pages. One-time setup: in the repo, go to
**Settings -> Pages -> Build and deployment -> Source** and select
**GitHub Actions**.

### TerpConnect (mirror — manual)

```bash
npm run deploy     # builds, then rsyncs dist/ into glue.umd.edu:~/../pub/
```

The deploy script does **not** use `rsync --delete`, so other files already in
`pub` (like the old `csterpconnect/` folder) are left alone. If you ever want
`pub` to exactly mirror `dist/`, add `--delete` to the rsync line in
`deploy.sh`.

You can also push to TerpConnect from CI by adding an SSH key as a repo secret
— a commented step at the bottom of `deploy.yml` shows how.

### If the TerpConnect page doesn't render after deploy

- Confirm files landed in `pub` (a sibling of home): `ssh aedan@glue.umd.edu 'ls -la ~/../pub'`
- Fix permissions: `ssh aedan@glue.umd.edu 'chmod -R a+rX ~/../pub'`
- The default page must be `index.html` (lowercase).

## First push

```bash
git init
git add .
git commit -m "Initial homepage with UMD Design System"
gh repo create aedan-umd/aedan-umd.github.io --public --source=. --push
```

Then enable **Settings -> Pages -> Source: GitHub Actions** (one time).

## Fonts

The official UMD families (`Interstate`, `Crimson Pro`, `Barlow Condensed`)
are licensed and not on Google Fonts. `src/styles.css` declares them with
system fallbacks. To use the real faces, drop the font files in `public/fonts/`
and add `@font-face` rules.

## Adding projects

Each project is a `<umd-element-card>` block in `index.html`. A commented
template is included there — copy it, fill in the repo URL, headline, and
description.
