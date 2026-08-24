---
title: Usage
linkTitle: Usage
weight: 40
description: >
  From an empty catalog to a library you actually use.
---

## First run

### 1. Create your account

Alexandria has exactly one owner. On first launch you register that account
with an email address and a password.

The address is a **login identifier and nothing else** — Alexandria sends no
mail of any kind. In place of email recovery, registration mints **ten
single-use recovery codes**. They are shown once. Save them somewhere outside
Alexandria; each one gets you back in exactly once, and you can ask for a fresh
set at any time.

That is the local account the desktop application uses. A server running in
external mode instead defers to
[Heimdall](https://github.com/artur-rios/heimdall-api), where the account is
registered and where verification, password recovery, and two-factor all
happen; there is nothing to register in Alexandria, and none of this section
applies. See [Architecture]({{< relref "/docs/architecture" >}}).

### 2. Register a library folder

Alexandria does not go looking for files on its own. You tell it which folders
are yours, and it confines itself to those. You can register more than one.

### 3. Run the first index

Indexing walks each registered folder, classifies every file it recognises by
type, records what the directory listing already knows about it — name, size,
and when it was last modified — and reads type-specific metadata out of the
file's own tags.

It does **not** read your files' contents to identify them, so how long a scan
takes depends on how many files you have rather than how large they are.

A scan runs in the background and you can watch it: it reports how far along it
is, and you can pause it, pick it up again later, or abandon it. Start a big one
at **low** priority to keep the application responsive while it works through.
Closing the application does not lose a scan — it is offered back at the next
launch.

Re-run a scan whenever your folders change. A re-scan visits everything already
in the catalog and compares each file's size and modification time; anything
that has gone is flagged as missing rather than deleted. Your own metadata edits
are never overwritten. See [Architecture]({{< relref "/docs/architecture" >}})
for both pipelines.

## Browsing

The catalog is organised by file type: music, movies, series, HTML pages,
Markdown and text notes, PDFs and e-books, comic books, images, and bookmarks.

Each type can be viewed as a **list**, a **detailed list**, or a **grid** —
pick whichever suits what you are looking at. Search, filtering, and sorting
work across the whole catalog, not just the type you are currently in.

## Playing and reading

**Video.** Full screen, seeking, subtitle track selection, and audio track
selection.

**Audio.** A persistent player that stays with you as you browse. A disc,
vinyl, or tape animation turns for the duration of an album or artist and stops
when you pause.

**Documents and images.** PDFs, e-books, comic books, images, saved HTML pages,
and rendered Markdown all open inside the application.

## Editing

Alexandria edits **metadata and text**. It never edits media.

**Text and Markdown** are edited in place, with a live preview beside the
editor. Changes are written back to the file on disk.

**Music and video metadata** can be edited from the file's detail view.
Alexandria rewrites the metadata; it never re-encodes the media.

**Renaming** works on any file and renames it on disk.

## Organising

**Collections** group any files and bookmarks together, across types. A file
can belong to more than one.

**Watchlists** track movies and series, with **per-episode progress** — so a
series remembers where you left off.

**Reading lists** track books and comics, with **per-issue progress**.

## Deleting safely

Deletion is two-phase, and removing a file from disk is always a separate,
explicit act.

| Action | What happens to the catalog record | What happens to the file |
|---|---|---|
| **Soft delete** | Hidden, recoverable for the retention window | Untouched |
| **Restore** | Visible again | Untouched |
| **Purge record** | Removed from the catalog | Untouched |
| **Purge on disk** | Removed from the catalog | **Deleted**, after an explicit confirmation |

If you soft-delete something by mistake, restore it. The file was never in
danger.

## Getting back in

Log in with your email and password. If you have lost the password, use one of
the ten recovery codes from registration — each works once. You can regenerate
the full set at any time, which invalidates the old ones.

## Preferences

Alexandria adapts to window and screen size, ships **light and dark themes**,
and is translated into **Brazilian Portuguese and English**.

## Using the HTTP API instead

If you are driving Alexandria from your own client rather than the desktop
application, the REST/JSON surface is documented in the API repository's README
and its
[Use Case Specification Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Use%20Case%20Specification%20Document.md).
Start with [Installation]({{< relref "/docs/installation" >}}) for how to run
the server.
