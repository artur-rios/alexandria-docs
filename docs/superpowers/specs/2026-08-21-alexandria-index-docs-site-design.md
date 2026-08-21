# Alexandria documentation portal — design

- **Date:** 2026-08-21
- **Repository:** `alexandria-index`
- **Status:** approved

## 1. Problem

Alexandria is spread across two code repositories with no shared entry point.
`alexandria-api` holds the Rust core, the HTTP server, and the FFI surface;
`alexandria-ui` holds the Flutter desktop front-end and the release pipeline
that packages both halves together. Each has a long README and a full set of
requirements documents, but nothing introduces the project as a whole, explains
how the pieces relate, or tells a newcomer how to install and use it.

`alexandria-index` is an empty repository. It becomes that entry point.

## 2. Scope

A Hugo site using the Docsy theme, deployed to GitHub Pages, covering:

- an overview of the project,
- its architecture, illustrated with Mermaid diagrams,
- installation instructions for end users and for building from source,
- usage instructions,
- a map of the project's repositories and their canonical documents.

### 2.1 Explicitly out of scope

The site builds no software and ships no new installer.

`alexandria-ui`'s release workflow already produces a Windows installer
executable (Inno Setup, from `packaging/windows/installer.iss`) plus an MSIX,
and Linux `.deb`, `.AppImage`, and `.flatpak` packages. Every one of them
bundles the compiled `alexandria-ffi` shared library beside the Flutter
executable, and the desktop application links that core in process. A single
install therefore already delivers both the front-end and the back-end, ready
to use, with no service to configure and no second component to install.

Building a new installer in this repository would duplicate that pipeline. The
site documents and links the existing artifacts instead.

The `alexandria-http` server has no packaging today. Packaging it is a separate
piece of work and is not part of this design.

## 3. Site structure

```
alexandria-index/
├── go.mod, go.sum              # Docsy pulled as a Hugo Module
├── hugo.toml                   # baseURL, Mermaid, menus, repository links
├── package.json                # autoprefixer + postcss-cli for Docsy's SCSS
├── content/en/
│   ├── _index.md               # landing page
│   ├── docs/
│   │   ├── _index.md
│   │   ├── overview/
│   │   ├── architecture/
│   │   ├── installation/
│   │   ├── usage/
│   │   └── repositories/
│   └── about/_index.md
├── assets/scss/                # colour and typography overrides
├── static/                     # favicon and images
└── .github/workflows/deploy.yml
```

Content lives under `content/en/` so a `pt-BR` tree can be added later without
restructuring. The site ships English only for now.

## 4. Content plan

### 4.1 Overview

What Alexandria is: a single-user personal library that indexes, organises, and
surfaces on-disk media and documents — audio, movies and series, saved HTML,
Markdown and text, PDFs and e-books, comic books, images, and browser
bookmarks. It records metadata plus a path and content hash. It never
re-encodes, duplicates, or relocates a file.

What it is not: no cloud, no synchronisation, no sharing, no second account, no
media editing, no mobile, web, or macOS build.

### 4.2 Architecture

Four Mermaid diagrams:

1. **Component graph** — the three crates (`alexandria-core`,
   `alexandria-http`, `alexandria-ffi`), the two transports over the one core,
   and the SQLite database, filesystem, and optional external auth service.
   Adapted from `alexandria-api`'s README.
2. **Deployment modes** — the desktop application linking the core in process
   over FFI, contrasted with a client talking to the axum REST/JSON server.
3. **Indexing pipeline** — discover, classify by type, hash, persist.
4. **Deletion lifecycle** — a state diagram covering soft delete, restore, and
   hard purge, plus the separate explicit purge-on-disk.

### 4.3 Installation

End-user path first: download the release asset for the platform, run it, note
that there is no separate back-end to install, and say where the catalog and
settings live.

Then a build-from-source path per repository, with the prerequisites the
repositories actually state: Rust 1.94 or later, the Flutter SDK with the
desktop target enabled, the Visual Studio C++ desktop workload on Windows, GTK
development packages on Ubuntu, and the ffmpeg development libraries. Building
`alexandria-ffi` and placing the shared library under `native/windows/` or
`native/linux/` — or pointing `ALEXANDRIA_CORE_LIBRARY` at it — is called out
explicitly, because a development checkout does not carry it.

### 4.4 Usage

Task-oriented walkthroughs: registering a library folder, running the first
index and later re-scans, browsing by file type in list, detailed-list, and
grid layouts, playing video and audio, reading documents and comics, editing
Markdown and text in place, editing music and video metadata, renaming files,
organising collections, watchlists with per-episode progress, and reading lists
with per-issue progress, searching and filtering, the delete/restore/purge
flow, and account registration with the ten single-use recovery codes.

### 4.5 Repositories

A table of the three repositories — what each contains and where its canonical
documents are — with deep links into each repository's `docs/initial/` and
`docs/requirements/` trees rather than copies of their content.

## 5. Build and deployment

Docsy is consumed as a Hugo Module, which requires the Go toolchain locally and
in CI. `.github/workflows/deploy.yml` runs on push to `main`: check out, set up
Go, set up Hugo extended, `npm ci`, `hugo --minify`, and deploy to GitHub Pages.

`baseURL` is `https://artur-rios.github.io/alexandria-index/`.

## 6. Correctness rule

Every factual claim on the site is sourced from the two code repositories'
READMEs and requirements documents. No feature, version, endpoint, or command
is invented. Where a detail cannot be verified from those sources, the page
links out to them instead of asserting it.

## 7. Assumptions

1. The GitHub remote will be `artur-rios/alexandria-index`. The repository
   currently has no remote and no commits; the workflow is written for that
   name, and the owner creates the remote and enables GitHub Pages.
2. The Go toolchain is installed locally so `hugo server` works.
3. The site is English-only at launch, with the directory structure in place
   for a later pt-BR translation.
