# alexandria-docs

The documentation site for [Alexandria](https://github.com/artur-rios/alexandria-api),
a single-user personal library that indexes, organises, and surfaces your
on-disk media and documents.

**[docs.artur-rios.tech/alexandria-docs](https://docs.artur-rios.tech/alexandria-docs/)**

This repository contains documentation only. It builds no software and ships no
installer. Every package — the Windows installer, MSIX and portable `.zip`, and
the Linux `.sh` installer, `.tar.gz`, `.deb`, `.AppImage` and `.flatpak` — is
built and published as a
[release](https://github.com/artur-rios/alexandria-ui/releases) by
[alexandria-ui](https://github.com/artur-rios/alexandria-ui)'s tagged release
workflow, and each one already bundles the Rust core.

## The project

| Repository | What it holds |
|---|---|
| [alexandria-api](https://github.com/artur-rios/alexandria-api) | The Rust core, the REST/JSON server, and the FFI surface. |
| [alexandria-ui](https://github.com/artur-rios/alexandria-ui) | The Flutter desktop front-end and the release packaging. |
| [alexandria-docs](https://github.com/artur-rios/alexandria-docs) | This site. |

## Running it locally

**Requirements:** [Hugo extended](https://gohugo.io/installation/) 0.158 or
newer — the configuration uses `locale` and `languages.*.label`, which arrived
in that release; the site image builds with 0.165.0 — the [Go toolchain](https://go.dev/dl/) — Docsy is consumed as a Hugo
Module — and Node with npm, for Docsy's PostCSS pipeline.

```bash
git clone https://git.techcraftsmen.net/artur-rios/alexandria-docs.git
cd alexandria-docs
npm ci
hugo server
```

The site is then at <http://localhost:1313/>.

To reproduce what the image builds:

```bash
hugo --minify
```

## Layout

```txt
alexandria-docs/
├── hugo.toml                 # site configuration
├── go.mod                    # the Docsy theme, as a Hugo Module
├── package.json              # autoprefixer + postcss-cli
├── assets/scss/
│   └── _styles_project.scss  # light/dark code-block styles
├── content/en/
│   ├── _index.md             # landing page
│   ├── about/
│   └── docs/
│       ├── overview/
│       ├── architecture/     # Mermaid diagrams
│       ├── installation/
│       ├── usage/
│       └── repositories/
├── ci/build.sh               # the pull request check: builds the image
├── yggdrasil.yml             # what Jenkins checks
├── Dockerfile                # builds and serves the site
├── .dockerignore
├── renovate.json             # dependency updates
└── .gitattributes
```

Content lives under `content/en/` so a `pt-BR` translation can be added
alongside it without restructuring.

## Updating the theme

`go.mod` currently pins Docsy to v0.15.0 on purpose. v0.16.0 moved the actual
theme (`layouts/`, `assets/`) into a git submodule that Go's module zip does
not include, so it resolves as a Hugo Module but builds no output.

**Re-checked 2026-08-24 against v0.16.0, still the latest release: the fault is
unchanged.** It resolves, then fails at build with `template for shortcode
"blocks/cover" not found` and writes no `public/`. The pin stays.

Bumping the version therefore needs a real check, not just a green `go get`:

```bash
hugo mod get -u github.com/google/docsy
hugo --minify
```

Only commit the resulting `go.mod` and `go.sum` once that build has actually
produced `public/index.html` — an empty or missing `public/` means the new
version is broken the same way v0.16.0 was, and the pin should stay.

## Deployment

The site is deployed by Jenkins, from the yggdrasil pipeline: every push to
`main` builds the `Dockerfile` and deploys the image to production, where it
is served at <https://docs.artur-rios.tech/alexandria-docs/>. The image answers
`/healthz` for the status check. The address comes from the build arguments
`PUBLIC_HOST` and `PUBLIC_PATH`, which the deploy passes, so the `baseURL` in
`hugo.toml` only matters for `hugo server`.

Every pull request is checked by Jenkins' `build` check (`yggdrasil.yml`, run by
`ci/build.sh`), which builds the same image without pushing or running it.

To build the image locally:

```bash
docker build -t alexandria-docs \
  --build-arg PUBLIC_HOST=docs.artur-rios.tech --build-arg PUBLIC_PATH=/alexandria-docs .
docker run --rm -p 8080:8080 alexandria-docs
```

The site is then at <http://localhost:8080/alexandria-docs/>.

## Contributing

The GitHub repository is a read-only mirror for the real, open-source code of alexandria-docs. Its home is Forgejo, at
https://git.techcraftsmen.net/artur-rios/alexandria-docs. If you want to contribute, e-mail me at arturdev@duck.com.
