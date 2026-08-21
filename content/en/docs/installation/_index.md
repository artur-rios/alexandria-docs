---
title: Installation
linkTitle: Installation
weight: 30
description: >
  Install the desktop application, or build the whole project from source.
---

## Install the application

This is what almost everyone wants. The desktop release **already contains the
core** — the Rust library is bundled beside the executable and linked in
process. There is no server to install, no service to configure, and no second
component to keep running.

Releases are published from the front-end repository:

**[Download from alexandria-ui releases](https://github.com/artur-rios/alexandria-ui/releases)**

### Windows

Windows 10 x64 or later.

| Asset | How to install |
|---|---|
| `alexandria-setup-<version>.exe` | Run it. It installs into your own user profile, adds a Start menu entry, and offers a desktop shortcut. |
| `.msix` | Install through the Windows package installer, if you prefer a packaged app. |

The installer is currently **unsigned**, so Windows SmartScreen will warn on
first run. Choose *More info* and then *Run anyway* if you trust the source.

### Linux

Ubuntu LTS x64.

| Asset | How to install |
|---|---|
| `.deb` | `sudo apt install ./alexandria_<version>_amd64.deb` — it declares the distribution's ffmpeg runtime packages as dependencies, so apt pulls them in. |
| `.AppImage` | Mark it executable with `chmod +x` and run it. Nothing is installed system-wide. |
| `.flatpak` | `flatpak install ./alexandria-<version>.flatpak` |

### After installing

Launch Alexandria and go to [Usage]({{< relref "/docs/usage" >}}). Your catalog
and settings live in your own user profile, not alongside the application, so
uninstalling never touches them — and never touches your library files.

## Build from source

You only need this if you want to work on Alexandria.

### The core — `alexandria-api`

**Requirements:** Rust **1.94** or newer (edition 2021) and `cargo`. The floor
comes from sqlx 0.9, the highest MSRV in the dependency graph.

`alexandria-core` links against `ffmpeg-next` for video metadata extraction, so
the **ffmpeg C development libraries** and `clang` (for bindgen) must be
installed before the workspace will build. This is the project's only system
dependency, and without it `cargo build` and `cargo test` fail for the whole
workspace — not just the video code. Any ffmpeg from **3.0 to 9.0** works.

On Debian or Ubuntu:

```bash
sudo apt-get install libavformat-dev libavcodec-dev libavutil-dev \
  libavfilter-dev libavdevice-dev libswscale-dev libswresample-dev \
  pkg-config clang
```

Windows needs a build of ffmpeg that ships `include/` and `lib/` — not just
`bin/`. Most downloads labelled "essentials" ship the executables only and will
not work. The repository's README walks through the options in detail:

**[alexandria-api — Building](https://github.com/artur-rios/alexandria-api#building)**

Then:

```bash
git clone https://github.com/artur-rios/alexandria-api.git
cd alexandria-api
cargo build --release
```

### The front-end — `alexandria-ui`

**Requirements:**

- The Flutter SDK, with the desktop target for your platform enabled.
- **Windows:** Windows 10 x64 or later, with the Visual Studio "Desktop
  development with C++" workload.
- **Linux:** Ubuntu LTS x64, with the GTK development packages.
- A build of `alexandria-ffi`. Release packages bundle it; a development
  checkout does not.

```bash
git clone https://github.com/artur-rios/alexandria-ui.git
cd alexandria-ui
flutter pub get
dart run build_runner build
dart run ffigen --config ffigen.yaml
flutter gen-l10n
```

Build the core's shared library and put it where the loader looks —
`native/windows/` or `native/linux/` — or point `ALEXANDRIA_CORE_LIBRARY` at a
locally built one:

```bash
cd ../alexandria-api
cargo build --release -p alexandria-ffi
cp target/release/libalexandria_ffi.so ../alexandria-ui/native/linux/
```

Then run it:

```bash
flutter run -d windows
```

```bash
flutter run -d linux
```

### The HTTP server

Only needed if you want the REST/JSON surface rather than the desktop
application. Configuration is read from `config.toml` at startup, and any key
can be overridden with an `ALEXANDRIA_*` environment variable.

```bash
cp config.toml.example config.toml
cargo run --release -p alexandria-http
```

It binds to loopback by default. Check it is up:

```bash
curl http://127.0.0.1:8080/health
```

The full configuration surface — authentication mode and session TTL, bind
address, SQLite path, filesystem root, indexing concurrency, soft-delete
retention, and log level — is documented in
[`config.toml.example`](https://github.com/artur-rios/alexandria-api/blob/main/config.toml.example)
and in the
[Operations & Infrastructure Document](https://github.com/artur-rios/alexandria-api/blob/main/docs/requirements/Operations%20%26%20Infrastructure%20Document.md).

The server has no packaged release. Build it from source.
