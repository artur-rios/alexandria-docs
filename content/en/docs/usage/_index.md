---
title: Usage
linkTitle: Usage
weight: 40
description: >
  From an empty catalog to a library you actually use.
---

## First run

There is no account to create and no password to type: Alexandria opens
straight into the application.

### 1. Add a library

Alexandria does not go looking for files on its own. You tell it which folders
are yours, and it confines itself to those. Choose **Library**, then **Add
library…**, and pick a folder. You can add as many libraries as you like. If you
have none yet, Home guides you to this step.

### 2. Run the first index

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

## The left menu

The menu on the left lists, top to bottom: **Home**, **Bookmarks**, your
**pinned** libraries in alphabetical order, a divider, and your other libraries
in alphabetical order. There are no entries per file type. If the window is
narrow the menu scrolls, so no library is ever cut off.

Right-click a library (or long-press it) for **Pin** or **Unpin**, **Add to
favorites** or **Remove from favorites**, and **Rename…**. The choices are kept
by the core and survive a restart.

## Home

Home shows your **favorite** libraries, in alphabetical order, and the totals
across every library: how many libraries you have, and how many files of each
type, zeroes included. With no libraries at all it guides you to add one.

## Browsing a library

Click a library to open it in the main area as its folders. The header shows
the library's name, the **Pin** and **Favorite** toggles, and a menu with
**Rename**, **Scan again**, **Move…** and **Remove**. Breadcrumbs show where you
are, and folders come first, then files.

Show the contents as a **grid** or a **list**, filter by one file type or show
all of them, and pick a sort order. That choice is the same for every library.
Every action on a file is here: open, play, read, edit, rename, delete, and
details. Search works across every library, not just the one you are in.

## Managing libraries

The **Library** menu in the menu bar starts with **Add library…** and **Manage
libraries…**. The latter lists every library, with rename, scan again, move,
and remove for each. A library can be renamed at any time, even while it is
being scanned.

## Playing and reading

**Video.** Full screen, seeking, subtitle track selection, and audio track
selection.

**Documents and images.** PDFs, e-books, comic books, images, saved HTML pages,
and rendered Markdown all open inside the application.

## Editing

Alexandria edits **metadata and text**. It never edits media.

**Text and Markdown** are edited in place, with a live preview beside the
editor. Changes are written back to the file on disk.

**Video metadata** can be edited from the file's detail view.
Alexandria rewrites the metadata; it never re-encodes the media.

**Renaming** works on any file and renames it on disk.

## Organising

**Collections** group any files and bookmarks together, across libraries. A file
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

Deleted files are reached through **Deleted items** in the menu bar. If you
soft-delete something by mistake, restore it there. The file was never in
danger.

## Preferences

Alexandria adapts to window and screen size, ships **light and dark themes**,
and is translated into **Brazilian Portuguese and English**.
