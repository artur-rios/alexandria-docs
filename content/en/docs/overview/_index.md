---
title: Overview
linkTitle: Overview
weight: 10
description: >
  What Alexandria is, what it handles, and what it deliberately does not do.
---

## What it is

Alexandria is a personal library system for a single person. It indexes the
media and documents already sitting on your disk, records what it finds in a
local catalog, and gives you one application to browse, play, read, edit, and
organise all of it.

Two properties shape everything else about it:

**It is single-user.** One owner, one account. There is no sharing, no second
profile, no roles, and no multi-tenancy anywhere in the design.

**It does not own your files.** Alexandria stores metadata and a path — never
your file's contents. It never re-encodes, transcodes, duplicates, or relocates
a file, and it removes one from disk only when you explicitly ask it to.

## What it handles

| Kind | Covers |
|---|---|
| Audio | Music files, browsed and played in a persistent player |
| Video | Movies and series, with subtitle and audio track selection |
| Documents | PDFs and e-books |
| Comics | Comic book archives, tracked per issue |
| Text | Markdown and plain text, editable in place with a live preview |
| Web | Saved HTML pages |
| Images | Viewed in list, detailed-list, and grid layouts |
| Bookmarks | Browser bookmarks, organised alongside everything else |

## What you can do with it

- Register one or more library folders and run indexing and re-scans from the
  interface.
- Browse the catalog by file type, and search, filter, and sort across all of
  it.
- Play video with full screen, seeking, subtitle tracks, and audio tracks.
- Play audio in a persistent player, with a disc, vinyl, or tape animation that
  turns while an album or artist plays and stops on pause.
- View PDFs, e-books, comic books, images, saved HTML, and rendered Markdown.
- Edit Markdown and text files in place, with a live preview.
- Edit music and video metadata, and rename any file.
- Group files and bookmarks into collections.
- Track movies and series in watchlists, with per-episode progress.
- Track books and comics in reading lists, with per-issue progress.
- Soft-delete, restore, and purge catalog records, and separately purge a file
  from disk.
- Register the owner account, log in, and recover access with one of ten
  single-use codes minted at registration.

The interface adapts to window and screen size, in light and dark themes, in
Brazilian Portuguese and English.

## What it does not do

Being clear about the boundaries is part of the design, not an omission.

- **No media editing of any kind.** No audio or video re-encoding, no
  transcoding, no image manipulation.
- **No file management.** Alexandria never moves, copies, or duplicates a file.
- **No second account, no sharing, no profiles, no roles** — inside Alexandria.
  There is one owner, and every catalog operation belongs to them. The local
  account the desktop application uses is that one account. In external mode
  Alexandria accepts anyone
  [Heimdall](https://github.com/artur-rios/heimdall-api) places in its scope,
  but they all act as the same single owner: Alexandria keeps no per-person
  state, draws no distinction between them, and honours none of Heimdall's own
  roles. Who may be the owner is decided there; what an owner may do is the
  same either way.
- **No mail from Alexandria**, in either authentication mode. With the local
  account the desktop application uses, the address is a login identifier and
  nothing else, and recovery goes through single-use codes rather than a
  message. In external mode the account belongs to
  [Heimdall](https://github.com/artur-rios/heimdall-api), which does send mail
  — email verification, password recovery, two-factor codes — but it sends it
  as the identity provider. Alexandria still sends none, and never sees a
  password. See [Architecture]({{< relref "/docs/architecture" >}}).
- **No server, no cloud, no synchronisation** in the desktop application. It
  runs entirely on your machine.
- **No mobile, web, or macOS build.** Windows and Ubuntu only.

## How it is specified

Alexandria is specified before it is built. Both code repositories carry a full
set of requirements documents — a vision document, a system requirements
document, a use case specification, and more. The
[Repositories]({{< relref "/docs/repositories" >}}) page maps them all.

## Next

- [Architecture]({{< relref "/docs/architecture" >}}) — how the pieces fit
  together.
- [Installation]({{< relref "/docs/installation" >}}) — getting it onto your
  machine.
- [Usage]({{< relref "/docs/usage" >}}) — getting it working once it is there.
