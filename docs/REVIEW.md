# Review — 2026-08-23

A full review of `alexandria-api`, `alexandria-ui`, and `alexandria-docs`:
implementation against documentation, bugs, inconsistent behavior, and
improvements. This file records what was found in **this** repository and what
was done about it. The two code repositories carry their own copy covering
theirs.

This repository ships no software, so the findings here are all of one kind:
**the site described a system that has changed underneath it.** Three of the
four were not merely out of date but actively wrong — a reader following them
would have drawn a false conclusion about how the product works or what it
costs.

Verified after the changes: `hugo --minify` builds 19 pages with no errors, and
`public/index.html` is produced — the check this repository's README specifies.

---

## Findings

### D-01 — The indexing diagram described a pipeline that no longer exists · **fixed**

The Architecture page's *How a file gets indexed* flowchart had

```
CLASSIFY -->|supported| HASH["Hash the file's bytes"]
LOOKUP -->|"yes, same hash"| TOUCH
LOOKUP -->|"yes, hash changed"| UPDATE
```

and closed with "The content hash is what lets a re-scan tell a changed file
from an unchanged one without trusting timestamps."

The core replaced full-file hashing with the size/modification-time pair
(`FR-FC-09`, `FR-FC-10`). Indexing reads no file bytes to identify a file, and a
re-scan compares exactly the timestamps the old text said it did not trust.

This was the most damaging item in the whole review. The cost model follows
directly from it — the real pipeline scales with **how many** files you have,
the documented one with **how big** they are — so a reader sizing up a large
library would have been wrong by orders of magnitude, and would have expected a
re-scan to re-read every byte it owns.

**Fixed** — the section is rewritten around the stat pair and now leads with that
property rather than burying it. It gained three things the page never had:

- a separate re-scan flowchart, since a re-scan is a different pipeline from an
  index and the old text claimed they were the same one;
- a run state diagram covering pause, resume, cancel, and the run that survives
  the application closing;
- the fact that metadata extraction happens only at first index, which is what
  guarantees a re-scan cannot overwrite an edit the owner made.

### D-02 — Two more pages carried the same stale claim · **fixed**

| Page | Claimed |
| --- | --- |
| Overview | "Alexandria stores metadata, a path, and a **content hash**" |
| Usage | "classifies every file it recognises by type, **hashes its bytes**" |
| Usage | "a re-scan … uses the **content hash** to tell a changed file from an unchanged one" |

**Fixed.** The Usage section also gained what a user actually needs to know now
that runs are observable: that a scan can be watched, paused, resumed, or
abandoned, that low priority exists for a large one, and that closing the
application does not lose it.

### D-03 — The Installation page told Linux users the packages were broken · **fixed**

It said:

> The Linux packages do not currently bundle or declare ffmpeg. The ffmpeg
> runtime libraries must already be present on your system, or the application
> will install and then fail to load the core.

None of that is true. The release workflow gives each package the answer that
suits it, and verifies the result in a bare `ubuntu:24.04` container that has no
ffmpeg at all:

| Package | ffmpeg comes from |
| --- | --- |
| `.deb` | apt, declared as a dependency — deliberately not bundled, since a bundled copy would shadow the system's and go unpatched |
| Flatpak | the `org.freedesktop.Platform.ffmpeg-full` runtime extension |
| installer, tarball, AppImage | bundled, along with the rest of the dependency closure |

Left alone, this would have sent people to install packages they do not need in
order to fix a failure that would not have happened.

**Fixed** — replaced with a per-asset table that names where each package's
ffmpeg comes from, and says why the `.deb` is deliberately the odd one out.

### D-04 — The Installation page said no release exists · **fixed**

It directed readers to the Actions tab for build artifacts and stated "A
published release is not available yet."

Tags `v0.0.1` and `v0.0.2` exist and the release workflow publishes a GitHub
Release from every tag. The page also omitted three shipped assets entirely: the
Windows portable `.zip`, the Linux `.tar.gz`, and the Linux self-extracting
`.sh` installer — and mis-described the Windows installer, which asks where to
install rather than placing itself in the user profile.

**Fixed** — points at Releases, lists every asset, and notes that sub-1.0
versions are published as prereleases.

### D-05 — Nothing explained how the two repositories connect · **fixed**

The site had a page for the core's architecture and a page listing the
repositories, but nothing showing the seam between them — which is the thing a
reader most needs a picture for, since the product is two repositories and one
program.

**Added** *How the front-end and the core fit together* to the Architecture
page, with three diagrams:

- **the call path**, from a screen through controllers, gateways, the client,
  the worker isolate and the generated bindings, into the C ABI and the core's
  handlers — and back;
- **the release pin**, showing how a tag on the front-end checks out the core at
  an exact commit and packages the two together;
- **a call end to end**, using the start of a scan, because it is the operation
  where the split between "answer immediately" and "work for minutes" is
  visible, and where the polling relationship between the two sides shows.

The prose names the three properties that keep the repositories from drifting:
nothing above the data layer touches `dart:ffi` and an analyzer rule enforces
it; every core call runs on a worker isolate; and the C header is generated,
vendored, and compared by CI so a signature change cannot land on one side only.

---

## Reviewed and found correct

- **Architecture — one core, two transports.** Accurate, including which
  transport the desktop application actually uses.
- **The authentication section.** The division of labour with Heimdall is
  correctly and carefully described, including that Alexandria never sees a
  password and honours none of Heimdall's roles.
- **The deletion lifecycle diagram.** Matches the implementation, including that
  purging a record leaves the file alone.
- **Overview — what it does not do.** The distinctions it draws about mail and
  about accounts in the two auth modes are exactly right and easy to get wrong.
- **The Docsy v0.15.0 pin and the reason recorded for it** in the README. The
  rationale still holds and the warning against bumping without checking
  `public/index.html` is worth keeping.
- **Repositories page.** Correct, and the link out to Heimdall's own
  documentation site is right.

---

## Not done, and why

- **No `pt-BR` translation was added.** Content is already laid out under
  `content/en/` so one can be added alongside without restructuring, but
  translating the site is a separate piece of work from correcting it.
- **The two Hugo deprecation warnings** (`.Language.LanguageDirection`,
  `.Site.AllPages`) come from the Docsy theme, not from this repository's
  content. They are pre-existing, harmless today, and fixing them means moving
  off the deliberate v0.15.0 pin.
