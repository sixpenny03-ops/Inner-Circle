# Security Policy

## Supported status

Inner Circle 0.9.0 Beta 1 is the only current development line. Security fixes are best-effort;
there is no stable or production-supported release. Reports should identify the commit and whether
the web client, native client, host application, or Node server is affected.

Inner Circle makes real end-to-end-encryption claims (Olm/Megolm via `vodozemac`, plus a custom
AES-256-GCM "epoch ring" scheme for persistent history). Please read [`DISCLAIMER.md`](DISCLAIMER.md)
for an honest account of what has and hasn't been independently verified — this is a hobby project,
not audited software, and it has not had a professional security review.

The source code may be published for review. The native client now pins the first certificate seen
for an exact server address and blocks later changes, but this source change still requires a clean
Windows build and end-to-end verification. See [`HARDENING.md`](HARDENING.md).

## Reporting a vulnerability

If you find a genuine security issue — something that breaks the encryption, leaks plaintext to
the server, lets someone bypass access controls, or otherwise undermines the privacy/security
claims this project makes — please report it privately rather than opening a public GitHub issue
or posting it in Discord/social media first.

To report privately:

- Use GitHub's private vulnerability reporting for this repository if it's enabled (check the
  repo's "Security" tab), **or**
- Contact the project owner directly through a private channel.

Please include:

- What you found and why it's a security issue (not just "this looks weird").
- Steps to reproduce it, ideally against a scratch/test circle rather than a real one.
- The affected component (host app / `server/`, native client / `client-tauri/`, web client /
  `server/server/public/`, or the shared crypto crate / `crypto-core/`).

## What's in scope

- The cryptographic implementation itself (`crypto-core/`, `client-tauri/src-tauri/src/crypto/`,
  `server/server/public/epoch.js`) — anything that could let the server, or a party other than the
  intended recipient, read plaintext it shouldn't be able to.
- Access-control logic (`server/server/dist/server.js`, `permissions.js`) — anything that lets a
  member do something their role shouldn't permit, or lets a removed/non-member reach content they
  shouldn't.
- Anything that could leak a private key, recovery code, or vault password.

## What's out of scope (for now)

This is a self-hosted, trusted-friends project, not a hardened multi-tenant service. Things like
"the host machine's admin could theoretically tamper with the running process" are inherent to the
self-hosting model and not considered a reportable vulnerability on their own — the threat model is
protecting against the *server* (and anyone who compromises it) reading message content, not
against a malicious *host operator* actively subverting their own installation.

The native client uses trust on first use for the host's generated self-signed certificate. The
first connection is not protected against an attacker already intercepting that connection. Every
later certificate change fails closed until the user deliberately removes the saved fingerprint.

## Response

This project is maintained by one person in their spare time, so response times will vary — but a
genuine, privately-reported security issue will be taken seriously and prioritized over other work.
