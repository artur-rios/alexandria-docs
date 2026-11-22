---
title: Architecture
linkTitle: Architecture
weight: 20
description: >
  One Rust core, one desktop application — and how a file
  travels through them.
---

## One core, one desktop application

Alexandria's domain logic lives in a single Rust library. A thin C ABI sits on
top of it, and the Flutter desktop application calls the core through that ABI,
in the same process. Nothing else calls the core: it has no network interface
and no notion of who is using it.

```mermaid
graph LR
    subgraph inproc["Desktop — one process"]
        direction TB
        A1["Flutter UI"] -->|C ABI| A2["alexandria-core"]
        A2 --> A3[("SQLite")]
        A2 -->|index / rename / write text| A4["Local filesystem"]
    end
```

`alexandria-core` follows a Command/Query baseline. Repository **traits** are
what make its handlers unit-testable; `alexandria-ffi` adds the C ABI and
nothing else.

There is no authentication anywhere: the application belongs to the person
using the machine, so the core has nothing to check. The core also keeps the
choices you make about your libraries — pinned, favorite, and name — so they
survive a restart, and it answers the catalog statistics shown on Home.

In desktop mode there is no network hop between the interface and its data, and
nothing to start, stop, or configure separately. That is why installing
Alexandria installs one thing — see
[Installation]({{< relref "/docs/installation" >}}).

## How a file gets indexed

Indexing **never reads a file's bytes to identify it**. It reads the size and
modification time that the directory listing already carries, and that pair is
what a re-scan compares. This is the single most important thing to know about
the pipeline, because the cost follows from it: a scan's duration is set by how
*many* files you have, not by how *big* they are.

```mermaid
flowchart TD
    START(["Index a registered folder"]) --> WALK["Walk the folder tree"]
    WALK --> CLASSIFY{"Extension<br/>recognised?"}
    CLASSIFY -->|"no"| SKIP["Skipped"]
    CLASSIFY -->|"yes"| LOOKUP{"Path already<br/>in the catalog?"}
    LOOKUP -->|"yes"| ALREADY["Already cataloged<br/>— left alone"]
    LOOKUP -->|"no"| INSERT["Insert a record:<br/>path · name · type<br/>· size · modified time"]
    INSERT --> META["Extract type-specific metadata<br/>from the file's own tags"]
    META --> DONE(["Catalog current"])
    INSERT -->|"this one file failed"| BAD["Counted and logged<br/>— the scan continues"]
```

Metadata extraction runs **only at first index**, which is what guarantees a
re-scan can never overwrite something you edited yourself.

### What a re-scan does

A re-scan visits every path already in the catalog. It discovers nothing new —
that is what indexing a folder is for — and it compares the stat pair.

```mermaid
flowchart TD
    START(["Re-scan the catalog"]) --> EACH["For each cataloged path"]
    EACH --> THERE{"Still on disk?"}
    THERE -->|"no"| MISS["Flag it as missing.<br/>The record stays, and stays yours."]
    THERE -->|"yes"| CHANGED{"Size or modified<br/>time different?"}
    CHANGED -->|"no"| SAME["Unchanged — nothing written"]
    CHANGED -->|"yes"| UPD["Update size, modified time,<br/>and when it was last seen"]
```

A missing file is **flagged, never deleted**. Deleting is always something you
ask for.

### Following a scan while it runs

A large library takes a while, so a scan is a **run** you can watch and control
rather than something you start and hope about.

```mermaid
stateDiagram-v2
    [*] --> Running: start
    Running --> Complete: finished the walk
    Running --> Failed: the folder could not be read at all
    Running --> Paused: pause
    Paused --> Running: resume
    Running --> Cancelled: cancel
    Paused --> Cancelled: cancel

    note right of Paused
        Keeps its place. Closing the
        application leaves a run here,
        and it is offered back at the
        next launch.
    end note

    note right of Cancelled
        Abandoned for good.
    end note
```

A run reports how far along it is, and can be paced: start it at **low**
priority — or pause and resume it at low priority — to keep the application
responsive while a big scan works through in the background. Nothing ever
resumes by itself.

A single unreadable file is counted and skipped; it never abandons the rest of
the scan.

## How the front-end and the core fit together

The desktop application and the core are two repositories and one program. This
is the seam between them, in the direction a call actually travels.

```mermaid
graph TD
    subgraph ui["alexandria-ui — Flutter"]
        direction TB
        SCR["Screens<br/>presentation"]
        CTL["Controllers<br/>application"]
        GW["Gateways<br/>data"]
        CC["CoreClient"]
        ISO["Worker isolate"]
        BIND["Generated bindings<br/>ffigen"]
    end

    subgraph api["alexandria-api — Rust"]
        direction TB
        ABI["C ABI<br/>alexandria-ffi"]
        CORE["Handlers<br/>alexandria-core"]
        DB[("SQLite")]
        FS["Your library<br/>on disk"]
    end

    SCR -->|"user acts"| CTL
    CTL -->|"typed call"| GW
    GW -->|"one method per operation"| CC
    CC -->|"message across a port"| ISO
    ISO -->|"dart:ffi"| BIND
    BIND -->|"C call, in process"| ABI
    ABI --> CORE
    CORE --> DB
    CORE --> FS
    CORE -.->|"JSON + status code"| ABI
    ABI -.-> BIND
    BIND -.-> ISO
    ISO -.->|"typed result or failure"| CC
    CC -.-> GW
    GW -.-> CTL
    CTL -.->|"state"| SCR
```

Three properties of that seam are worth stating, because they are what keep the
two repositories from drifting:

**Nothing above the data layer touches `dart:ffi`.** Screens and controllers see
typed domain objects and typed failures. An analyzer rule in the front-end's own
lint package enforces it, and a test suite proves the rule fires by running the
analyzer against deliberately-violating fixtures.

**Every core call runs on a worker isolate.** Indexing walks a filesystem and
listing the catalog serializes it all to JSON; either on the interface thread is
a frozen window. One long-lived isolate, not one per call.

**The C header is generated, vendored, and checked.** The core's build produces
the header with `cbindgen`; the front-end vendors a copy and generates its Dart
bindings from it with `ffigen`. CI compares the vendored copy against the core's
and fails on any difference, so a core signature change cannot land quietly on
one side.

### Which core the application is built against

The front-end pins the core to an exact commit — `CORE_REF`, in both its CI and
its release workflow, and the two must agree or the build stops.

```mermaid
graph LR
    TAG["A tag on alexandria-ui"] --> CI["Release workflow"]
    CI -->|"checkout CORE_REF"| SRC["alexandria-api at that commit"]
    SRC -->|"cargo build -p alexandria-ffi"| LIB["Shared library"]
    CI -->|"flutter build"| APP["Application bundle"]
    LIB --> PKG["One package"]
    APP --> PKG
    PKG --> REL["Release asset"]
```

Pinned to a commit rather than tracking a branch, so that moving to a newer core
is a deliberate act with the header re-vendored and the bindings regenerated in
the same change. It also means two builds of the same tag ship the same core.

### A call from end to end

Starting a scan, which is the operation with the most moving parts, and the one
where the split between "answer now" and "work for minutes" is visible.

```mermaid
sequenceDiagram
    autonumber
    participant U as Owner
    participant S as Libraries screen
    participant C as IndexRunsController
    participant G as Index gateway
    participant K as Core, over FFI
    participant W as Walk

    U->>S: Index this folder
    S->>C: startIndex(root)
    C->>G: startIndex(root, priority, session)
    G->>K: alexandria_index_start
    K->>K: validate the root
    K->>W: spawn the walk
    K-->>G: run id, immediately
    G-->>C: started
    C->>S: show it as running

    loop while the run is in flight
        C->>G: readRun(runId)
        G->>K: alexandria_index_run_status_json
        K-->>G: phase · processed · total · active time
        G-->>C: progress
        C->>S: update the bar
    end

    W-->>K: finished, with the tally
    C->>G: readRun(runId)
    G-->>C: complete, with counts
    C->>S: show the outcome until dismissed
```

The core publishes a status to be **asked for**, not a callback, so the
application polls while something is in flight and stops when nothing is. That
is also what lets a scan outlive the application: the run belongs to the core,
so its outcome is waiting to be read at the next launch rather than lost.

Pause, resume, and cancel travel the same path in the same direction — the
application asks, the core records, and the next reading reflects it.

## The deletion lifecycle

Nothing leaves your disk by accident. Deleting from the catalog and deleting
from disk are two separate operations, and the second is always explicit.

```mermaid
stateDiagram-v2
    [*] --> Active: indexed
    Active --> SoftDeleted: soft delete
    SoftDeleted --> Active: restore
    SoftDeleted --> [*]: purge the record
    Active --> [*]: purge on disk (explicit)

    note right of SoftDeleted
        The record is hidden but recoverable
        for the configured retention window.
        The file itself is untouched.
    end note
```

A soft delete hides the record and leaves the file alone. Purging the record
removes it from the catalog and still leaves the file alone. Purging on disk is
the only operation that removes the file itself, and it is a separate action
with its own confirmation.

## Where the detail lives

This page is a map, not a specification. The normative detail is in the code
repositories:

- [System Requirements Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/System%20Requirements%20Document.md)
  — the functional and non-functional requirements, the data model, and the
  traceability matrix.
- [Use Case Specification Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Use%20Case%20Specification%20Document.md)
  — every use case, its flows, and its alternatives.
- [Business Rules](https://github.com/artur-rios/alexandria-api/blob/main/docs/initial/Business%20Rules.md)
  — the domain entities and the rules that govern them.

The [Repositories]({{< relref "/docs/repositories" >}}) page lists the rest.
