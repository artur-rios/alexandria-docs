# alexandria-docs

The documentation site for [Alexandria](https://github.com/artur-rios/alexandria-api),
a single-user personal library that indexes, organises, and surfaces your
on-disk media and documents.

**[artur-rios.github.io/alexandria-docs](https://artur-rios.github.io/alexandria-docs/)**

This repository contains documentation only. It builds no software and ships no
installer — the Windows installer executable and the Linux `.deb`, `.AppImage`,
and `.flatpak` packages are built by
[alexandria-ui](https://github.com/artur-rios/alexandria-ui/actions)'s tagged
release workflow, available as build artifacts from that workflow run, and
each of them already bundles the Rust core.

## The project

| Repository | What it holds |
|---|---|
| [alexandria-api](https://github.com/artur-rios/alexandria-api) | The Rust core, the REST/JSON server, and the FFI surface. |
| [alexandria-ui](https://github.com/artur-rios/alexandria-ui) | The Flutter desktop front-end and the release packaging. |
| [alexandria-docs](https://github.com/artur-rios/alexandria-docs) | This site. |

## Running it locally

**Requirements:** [Hugo extended](https://gohugo.io/installation/) 0.126 or
newer, the [Go toolchain](https://go.dev/dl/) — Docsy is consumed as a Hugo
Module — and Node with npm, for Docsy's PostCSS pipeline.

```bash
git clone https://github.com/artur-rios/alexandria-docs.git
cd alexandria-docs
npm ci
hugo server
```

The site is then at <http://localhost:1313/>.

To reproduce what CI builds:

```bash
hugo --minify
```

## Layout

```txt
alexandria-docs/
├── hugo.toml                 # site configuration
├── go.mod                    # the Docsy theme, as a Hugo Module
├── package.json              # autoprefixer + postcss-cli
├── content/en/
│   ├── _index.md             # landing page
│   ├── about/
│   └── docs/
│       ├── overview/
│       ├── architecture/     # Mermaid diagrams
│       ├── installation/
│       ├── usage/
│       └── repositories/
└── .github/workflows/deploy.yml
```

Content lives under `content/en/` so a `pt-BR` translation can be added
alongside it without restructuring.

## Updating the theme

`go.mod` currently pins Docsy to v0.15.0 on purpose. v0.16.0 moved the actual
theme (`layouts/`, `assets/`) into a git submodule that Go's module zip does
not include, so it resolves as a Hugo Module but builds no output. Bumping the
version therefore needs a real check, not just a green `go get`:

```bash
hugo mod get -u github.com/google/docsy
hugo --minify
```

Only commit the resulting `go.mod` and `go.sum` once that build has actually
produced `public/index.html` — an empty or missing `public/` means the new
version is broken the same way v0.16.0 was, and the pin should stay.

## Deployment

Every push to `main` builds the site and publishes it to GitHub Pages. Pages
must be configured once, in the repository's Settings → Pages, with **Source**
set to **GitHub Actions**.
