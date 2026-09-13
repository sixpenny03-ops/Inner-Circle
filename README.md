# Inner Circle — 0.9.0 Beta 1

![Status: beta](https://img.shields.io/badge/status-beta-orange)
![Windows CI](https://github.com/sixpenny03-ops/Inner-Circle/actions/workflows/windows-ci.yml/badge.svg)

A small, self-hosted, end-to-end-encrypted group chat system. One person hosts a "circle";
others join it from a native client or a browser.

## Beta status

Inner Circle is **beta software**. The source is ready for public development on GitHub, but APIs,
storage formats, setup steps, and user-facing behavior may still change. Use it for testing and
low-stakes conversations among trusted people. Do not use it where failure could expose regulated,
safety-critical, legally privileged, or otherwise high-consequence information.

Before creating a release, complete [`RELEASE-CHECKLIST.md`](RELEASE-CHECKLIST.md). Current known
limitations and verification boundaries are recorded in [`BETA.md`](BETA.md),
[`HARDENING.md`](HARDENING.md), and [`DISCLAIMER.md`](DISCLAIMER.md).

Read [`DISCLAIMER.md`](DISCLAIMER.md) before running this or sharing it with anyone — this project
was built almost entirely by an AI assistant working from the owner's direction, and it has not
had an independent security review.

> **Publication and release warning:** Publishing this source code for inspection and collaboration
> is fine. However, do not advertise or distribute the current project as a production-ready secure
> messenger until the current source has been compiled and exercised on Windows and independently
> reviewed. The native client now uses persistent trust-on-first-use certificate pinning; a changed
> certificate is blocked and requires an explicit reset. Keep [`DISCLAIMER.md`](DISCLAIMER.md),
> [`SECURITY.md`](SECURITY.md), and [`HARDENING.md`](HARDENING.md) prominently included in every
> public copy or release until certificate authentication is independently reviewed.

## Platform

This repository's source is cross-platform (Rust, C++/wxWidgets, Node.js), but **the only builds
actually produced, tested, and distributed by this project are Windows 64-bit.** `server/build.bat`
and `server/run.bat` are Windows batch scripts (MSVC + vcpkg) with no Linux/macOS equivalent shipped
here, and the `client-tauri` binaries this project has released were compiled and tested on Windows
only. Building either app on Linux or macOS from this same source is possible in principle —
`cargo build` in `client-tauri/src-tauri` targets whatever platform it's run on, and `server/`'s
CMake has some Linux-specific branching from earlier development — but neither path is packaged,
scripted, or tested for you here. If you need it on another platform, expect to do some of that
work yourself.

## Layout

Three folders. `server/` is fully self-contained; `client-tauri/` depends on `crypto-core/` via a
relative path dependency, so keep those two together if you move or copy this project.

**`server/`** — the host/operator app: what you run to create and host a circle. A native
wxWidgets/C++ app that bundles an actual Node.js server (`server/server/`). This is where all
server/circle-wide settings live (multi-device, device retirement, invite permissions,
persistent-history settings, and more) — never in either client. Run `server/build.bat` to build
and launch it (Windows + vcpkg).

**`client-tauri/`** — the member client: what a person joining a circle uses day to day. A Rust
(Tauri) backend with an HTML/CSS/JS webview frontend. Depends on `crypto-core/` below via a
relative path dependency.

**`crypto-core/`** — the shared Rust crypto crate (Olm/Megolm wrapper via
[`vodozemac`](https://github.com/matrix-org/vodozemac), signing, local vault encryption) that
`client-tauri/` builds on. Pure Rust, testable anywhere with `cargo test`.

There's also a reference web client bundled inside `server/server/public/` — a plain HTML/JS
client kept at parity with the native app, useful if you'd rather not install anything.

## How the encryption works, briefly

1:1 messages use [Olm](https://gitlab.matrix.org/matrix-org/olm) (a double-ratchet design, the
same family of idea Signal uses) — each pair of devices does its own key exchange, and the key
rotates every message. Group messages use Megolm: one session key per group, but it's never just
handed around — each device gets its own copy encrypted through its own Olm session, so the server
only ever sees ciphertext. Removing someone from a group rotates the session to a new key that
only goes to whoever's left, so a removed member can't read new messages and can't retroactively
decrypt old ones either.

The host of a circle gets no special decryption power beyond being an ordinary member — there's
even a setting (`historyOwnerCanRead`) that can specifically exclude the owner's own device from a
persistent-history conversation's decryption keys, if that's wanted.

These claims assume participants authenticate device identity by comparing the app's safety
numbers through a separate trusted channel. Until that comparison happens, a malicious or
compromised server that controls initial device-key discovery may be able to substitute a key.
The native client pins the first certificate seen for each exact server address and blocks later
certificate changes. See [`HARDENING.md`](HARDENING.md) for its trust and recovery behavior.

See `PLAN.md` (kept locally, not part of this public repo by default — see below) for the full,
detailed design and testing history behind every feature.

## Building, testing, and contributing

See `server/README.txt` for build/test details on the host app, and each of
`client-tauri/src-tauri/` and `crypto-core/` for their own `cargo build`/`cargo test` instructions.
The bundled Node server under `server/server/` installs with `npm ci` and validates with
`npm run build`. The JavaScript files in `dist/` are the canonical server source in this release;
the original TypeScript inputs were not retained. The build command therefore performs a clean,
deterministic syntax validation rather than pretending to compile missing TypeScript files.
GitHub Actions runs the server tests and Rust tests on Windows for every push and pull request.
Do not merge or publish a release while that workflow is failing.

## Licensing

This project is licensed under the **GNU Affero General Public License v3.0 (AGPL-3.0)** — see
[`LICENSE`](LICENSE) for the full text. In short: you're free to use, modify, and redistribute
this software, including running it as a network service, as long as you make the corresponding
source code (including your modifications) available to anyone who interacts with it over a
network, under the same license.

**A separate commercial license is available** for anyone who wants to use this software (or build
on it) without AGPL's source-availability obligations — for example, incorporating it into a
closed-source product. If that's you, reach out to discuss terms.

Copyright (C) 2026 the project owner. All source files are covered by the license above unless a
file explicitly states otherwise.

## A note on `PLAN.md` and `HANDOFF.md`

This project keeps a very detailed internal build log (`PLAN.md`) and a short orientation doc
(`HANDOFF.md`) that were invaluable during development but read like internal working notes
rather than user-facing documentation. They're excluded from this public repo by default (see
`.gitignore`) — remove those two lines from `.gitignore` if you'd rather publish them as-is.
