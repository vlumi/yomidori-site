# yomidori.app

The website for **Yomidori** (ヨミドリ) — a reading companion for Japanese
paperbacks, for iPhone, iPad and Mac. Landing, screenshots, support and privacy
pages.

The app itself is open source (MIT) at
[github.com/vlumi/yomidori](https://github.com/vlumi/yomidori). **This site is
not**: the copy, the name and the artwork are all rights reserved (see the app's
[TRADEMARKS.md](https://github.com/vlumi/yomidori/blob/main/TRADEMARKS.md)).
The repository is public for transparency, not for reuse.

## Build

Static site, built with [Hugo Extended](https://gohugo.io/). No JS, no external
assets — plain HTML/CSS, in the app's own palette.

The Hugo version is **pinned** in [`.hugoversion`](.hugoversion). Hugo is a
build-time tool, not a runtime — there's no security reason to chase updates,
and a bump is the thing most likely to *break* the build. So pin it and update
**deliberately**: bump `.hugoversion`, run a local build, and commit only if
it's clean. (The box's nginx / OS / certbot are what stay current, not Hugo.)

```sh
hugo server        # local preview at http://localhost:1313
hugo               # one-off build into ./public
```

## Screenshots

The gallery reads its pictures from `assets/img/shots/<name>.png` and resizes
them at build time; a name without a file shows a labelled placeholder, so the
page lays out before the pictures exist. The names it expects are the ones in
[`content/screenshots.md`](content/screenshots.md); drop the App Store PNGs in
under those names and rebuild.

## Privacy page

[`content/privacy.md`](content/privacy.md) is the app repository's
`PRIVACY.md` under a front matter; when that changes, copy it over again rather
than editing here.

## Deploy

Served by nginx over HTTPS (Let's Encrypt / certbot) on the host for
`yomidori.app`. [`deploy.sh`](deploy.sh) does it in one step: it fetches the
**pinned** Hugo Extended (cached per-version under `~/.local/share/yomidori-hugo`,
no root, never the system Hugo), pulls, and builds straight into the web root.

```sh
./deploy.sh                          # pull, build, publish to /var/www/yomidori.app
WEBROOT=/some/other/path ./deploy.sh # override the publish dir
./deploy.sh --no-pull                # build the working tree as-is
```

The build writes directly into `WEBROOT` (`--cleanDestinationDir` removes stale
files), so **nginx `root` points at the web root itself** (e.g.
`root /var/www/yomidori.app;`), not a `public/` subdir — no repo or source files
ever live under the served path. [`nginx.conf.example`](nginx.conf.example) is
the server block to start from.
