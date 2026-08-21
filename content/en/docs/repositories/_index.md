---
title: Repositories
linkTitle: Repositories
weight: 50
description: >
  Where the code and the specifications actually live.
---

Alexandria is three repositories.

| Repository | What it holds |
|---|---|
| [alexandria-api](https://github.com/artur-rios/alexandria-api) | The Rust workspace: `alexandria-core` (the domain), `alexandria-http` (the axum REST/JSON server), and `alexandria-ffi` (the C ABI). |
| [alexandria-ui](https://github.com/artur-rios/alexandria-ui) | The Flutter desktop front-end, and the release pipeline that packages it together with the core for Windows and Ubuntu. |
| [alexandria-docs](https://github.com/artur-rios/alexandria-docs) | This site. |

One more repository sits next to those three without being part of Alexandria:

| Repository | What it holds |
|---|---|
| [heimdall-api](https://github.com/artur-rios/heimdall-api) | The identity API Alexandria validates tokens against in external mode. Its [documentation site](https://artur-rios.github.io/heimdall-api/) covers its overview, architecture, API reference, and requirements. |

## The specifications

Alexandria is specified before it is built, and both code repositories carry
the same two-tier document set: informal `initial/` documents for context, and
formal `requirements/` documents for the normative detail.

### Core — `alexandria-api`

| Document | What's in it |
|---|---|
| [Brainstorm](https://github.com/artur-rios/alexandria-api/blob/main/docs/initial/Brainstorm.md) | The original free-form notes. |
| [Project Overview](https://github.com/artur-rios/alexandria-api/blob/main/docs/initial/Project%20Overview.md) | What the project is, who it is for, and how success is measured. |
| [Technology Stack](https://github.com/artur-rios/alexandria-api/blob/main/docs/initial/Technology%20Stack.md) | The informal stack decisions. |
| [Workflow](https://github.com/artur-rios/alexandria-api/blob/main/docs/initial/Workflow.md) | How one use case is delivered, step by step. |
| [Business Rules](https://github.com/artur-rios/alexandria-api/blob/main/docs/initial/Business%20Rules.md) | Domain entities, relationships, and the `BR-xx` rules. |
| [Vision Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Vision%20Document.md) | Stakeholders, positioning, and the `F-xx` features. |
| [System Requirements Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/System%20Requirements%20Document.md) | The `FR-<AREA>-xx` and `NFR-xx` requirements, the data model, and traceability. |
| [Use Case Specification Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Use%20Case%20Specification%20Document.md) | The `UC-xx` use cases, their flows, and their `AF-xx` alternatives. |
| [Development Workflow Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Development%20Workflow%20Document.md) | The branch pattern, issue lifecycle, and Definition of Done. |
| [Testing Specification Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Testing%20Specification%20Document.md) | How tests are written, named, and run. |
| [Technology Stack Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Technology%20Stack%20Document.md) | The single source of truth for every technology and version. |
| [Operations & Infrastructure Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Operations%20%26%20Infrastructure%20Document.md) | Layout, configuration, logging, startup checks, and the `IR-xx` platform requirements. |

### Front-end — `alexandria-ui`

The front-end repository carries the same set, scoped to the desktop
application.

| Document | What's in it |
|---|---|
| [Brainstorm](https://github.com/artur-rios/alexandria-ui/blob/main/docs/initial/Brainstorm.md) | The original free-form notes. |
| [Project Overview](https://github.com/artur-rios/alexandria-ui/blob/main/docs/initial/Project%20Overview.md) | What the application is and who it is for. |
| [Technology Stack](https://github.com/artur-rios/alexandria-ui/blob/main/docs/initial/Technology%20Stack.md) | The informal stack decisions. |
| [Workflow](https://github.com/artur-rios/alexandria-ui/blob/main/docs/initial/Workflow.md) | How one use case is delivered, step by step. |
| [Business Rules](https://github.com/artur-rios/alexandria-ui/blob/main/docs/initial/Business%20Rules.md) | Domain entities, relationships, and the `BR-xx` rules. |
| [Vision Document](https://github.com/artur-rios/alexandria-ui/blob/main/docs/requirements/Vision%20Document.md) | Stakeholders, positioning, and the `F-xx` features. |
| [System Requirements Document](https://github.com/artur-rios/alexandria-ui/blob/main/docs/requirements/System%20Requirements%20Document.md) | The requirements, the data model, and traceability. |
| [Use Case Specification Document](https://github.com/artur-rios/alexandria-ui/blob/main/docs/requirements/Use%20Case%20Specification%20Document.md) | The `UC-xx` use cases and their flows. |
| [Development Workflow Document](https://github.com/artur-rios/alexandria-ui/blob/main/docs/requirements/Development%20Workflow%20Document.md) | The branch pattern, issue lifecycle, and Definition of Done. |
| [Testing Specification Document](https://github.com/artur-rios/alexandria-ui/blob/main/docs/requirements/Testing%20Specification%20Document.md) | How tests are written, named, and run. |
| [Technology Stack Document](https://github.com/artur-rios/alexandria-ui/blob/main/docs/requirements/Technology%20Stack%20Document.md) | Every technology and version. |
| [Operations & Infrastructure Document](https://github.com/artur-rios/alexandria-ui/blob/main/docs/requirements/Operations%20%26%20Infrastructure%20Document.md) | Layout, configuration, logging, and the `IR-xx` platform requirements. |

## Tracking progress

Work is tracked on the
[Alexandria API project board](https://github.com/users/artur-rios/projects/8),
grouped into milestones that mirror the feature groups in the System
Requirements Document. Each repository's README carries a live backlog table
whose issue references render their own open or closed state.
