---
title: Alexandria
linkTitle: Alexandria
---

{{< blocks/cover title="Alexandria" image_anchor="top" height="med" color="dark" >}}
<div class="mx-auto">
  <p class="lead mt-5">Your own library, built from the files you already have.</p>
  <a class="btn btn-lg btn-primary me-3 mb-4" href="{{< relref "/docs" >}}">
    Get started <i class="fas fa-arrow-alt-circle-right ms-2"></i>
  </a>
  <a class="btn btn-lg btn-secondary mb-4" href="https://github.com/artur-rios/alexandria-api">
    Source <i class="fab fa-github ms-2"></i>
  </a>
</div>
{{< /blocks/cover >}}

{{% blocks/lead color="primary" %}}
Alexandria indexes, organises, and surfaces one person's on-disk media and
documents — audio, movies and series, saved HTML pages, Markdown and text
files, PDFs and e-books, comic books, images, and browser bookmarks.

It records metadata plus a path and a content hash. It never re-encodes,
duplicates, or relocates a file.
{{% /blocks/lead %}}

{{% blocks/section color="dark" type="row" %}}

{{% blocks/feature icon="fa-solid fa-magnifying-glass" title="Index what you own" %}}
Point Alexandria at a folder. It walks it, classifies every file by type,
hashes its bytes, and keeps the catalog current across re-scans.
{{% /blocks/feature %}}

{{% blocks/feature icon="fa-solid fa-play" title="Read, watch, and listen" %}}
Video with seeking, subtitles, and audio tracks. A persistent audio player.
PDFs, e-books, comic books, images, saved HTML, and rendered Markdown.
{{% /blocks/feature %}}

{{% blocks/feature icon="fa-solid fa-layer-group" title="Organise it your way" %}}
Collections, watchlists with per-episode progress, and reading lists with
per-issue progress — across the whole catalog.
{{% /blocks/feature %}}

{{% /blocks/section %}}

{{% blocks/section color="light" type="row" %}}

{{% blocks/feature icon="fa-solid fa-laptop" title="One local application" %}}
The desktop front-end links the Rust core in process. No server, no cloud, no
synchronisation, and nothing to configure after you install it.
{{% /blocks/feature %}}

{{% blocks/feature icon="fa-solid fa-shield-halved" title="Your files stay yours" %}}
Deletion is two-phase — soft delete, then restore or purge — and removing a
file from disk always takes a separate, explicit action.
{{% /blocks/feature %}}

{{% blocks/feature icon="fa-brands fa-rust" title="One core, two surfaces" %}}
A REST/JSON server and a C ABI share the same Rust domain library, so the two
transports cannot drift apart.
{{% /blocks/feature %}}

{{% /blocks/section %}}
