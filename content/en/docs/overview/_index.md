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

**It is single-user.** One owner, on one machine, with no account to create and
no sign-in. There is no sharing, no second profile, no roles, and no
multi-tenancy anywhere in the design.

**It does not own your files.** Alexandria stores metadata and a path — never
your file's contents. It never re-encodes, transcodes, duplicates, or relocates
a file, and it removes one from disk only when you explicitly ask it to.

## What it handles

| Kind | Covers |
|---|---|
| Video | Movies and series, with subtitle and audio track selection |
| Documents | PDFs and e-books |
| Comics | Comic book archives, tracked per issue |
| Text | Markdown and plain text, editable in place with a live preview |
| Web | Saved HTML pages |
| Images | Viewed in list and grid layouts |
| Bookmarks | Browser bookmarks, organised alongside everything else |

## What you can do with it

- Add one or more libraries and scan them again whenever they change, from the
  Library menu.
- Pin and favorite libraries, and rename them at any time, even during a scan.
- Browse a library as its folders, in a grid or a list, filtered by type and
  sorted; search across every library.
- See your favorite libraries and the totals — how many libraries, and how
  many files of each type — on Home.
- Play video with full screen, seeking, subtitle tracks, and audio tracks.
- View PDFs, e-books, comic books, images, saved HTML, and rendered Markdown.
- Edit Markdown and text files in place, with a live preview.
- Edit video metadata, and rename any file.
- Group files and bookmarks into collections.
- Track movies and series in watchlists, with per-episode progress.
- Track books and comics in reading lists, with per-issue progress.
- Soft-delete, restore, and purge catalog records, and separately purge a file
  from disk.

The interface adapts to window and screen size, in light and dark themes, in
Brazilian Portuguese and English.

## What it does not do

Being clear about the boundaries is part of the design, not an omission.

- **No media editing of any kind.** No video re-encoding, no transcoding, no
  image manipulation.
- **No file management.** Alexandria never moves, copies, or duplicates a file.
- **No music.** Music lives in [Orpheus](https://github.com/artur-rios/orpheus),
  a separate project.
- **No accounts, no sharing, no profiles, no roles.** Alexandria opens straight
  into the application: there is nothing to register and no password to type. There is one owner, and every catalog operation belongs to them.
- **No mail from Alexandria.** It sends none and asks for no address.
- **No server, no cloud, no synchronisation.** It runs entirely on your
  machine, and the core has no network interface at all.
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
