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

**Requirements:** [Hugo extended](https://gohugo.io/installation/) 0.160.1 or
newer — the minimum of Docsy v0.18.1; the site image builds with 0.165.0 — the [Go toolchain](https://go.dev/dl/) — Docsy is consumed as a Hugo
Module — and Node with npm, for the theme's Bootstrap and Font Awesome and for Dart Sass.

```bash
git clone https://git.techcraftsmen.net/artur-rios/alexandria-docs.git
cd alexandria-docs
npm ci
export PATH="$PWD/node_modules/.bin:$PATH"
hugo server
```

Hugo runs Dart Sass, which Docsy compiles its SCSS with, as `sass` from the
`PATH`; `npm ci` installs it as the `sass-embedded` package, hence the `export`.
On Windows, npm also installs the pure-JavaScript `sass` package, whose `sass`
command shadows the embedded one and cannot serve Hugo: put
`node_modules/sass-embedded-win32-x64/dart-sass` on the `PATH` instead, or
[install Dart Sass](https://gohugo.io/functions/css/sass/#dart-sass).

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
├── package.json              # Dart Sass (sass-embedded), autoprefixer, postcss-cli
├── package-lock.json
├── packages/hugoautogen/      # the theme's npm packages (`hugo mod npm pack`)
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

Docsy is the Hugo Module `github.com/docsy/docsy/theme`, at the version in
`go.mod`. Since v0.16.0 the theme declares Bootstrap and Font Awesome as npm
packages, which `hugo mod npm pack` writes into the `packages/hugoautogen`
workspace of `package.json`. Moving the theme is therefore four commands, not
one, or the stylesheets are built against the previous theme's Bootstrap:

```bash
hugo mod get github.com/docsy/docsy/theme@<version>
hugo mod npm pack
npm install
hugo --minify
```

Commit `go.mod`, `go.sum`, `package.json`, `package-lock.json` and
`packages/` once that build has produced `public/index.html` without a
warning. Run `hugo mod npm pack` again after any change to `package.json`
too: Hugo warns that the npm dependencies are out of sync until it has.

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
