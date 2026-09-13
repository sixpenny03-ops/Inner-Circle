# Beta release checklist

Do not publish binaries or create a GitHub Release until every required item below passes for the
exact commit and artifacts being released.

## Automated verification

- [ ] The Windows GitHub Actions workflow is green for the release commit.
- [ ] `npm ci` completes from a clean checkout.
- [ ] Every `dist/test-*.js` server test passes.
- [ ] `cargo test --locked` passes in `crypto-core/`.
- [ ] `cargo test --locked` passes in `client-tauri/src-tauri/`.
- [ ] The native client and host application compile from a clean Windows checkout.

## Manual Windows verification

- [ ] Create, stop, restart, and remove a test circle.
- [ ] Register and log in through both the browser and native client.
- [ ] Confirm 1:1 messages, groups, persistent history, attachments, and recovery.
- [ ] Confirm the first native HTTPS connection saves a certificate fingerprint.
- [ ] Confirm reconnecting with the same certificate succeeds.
- [ ] Replace the test certificate and confirm REST and WebSocket connections fail closed.
- [ ] Confirm the warning shows expected and presented fingerprints.
- [ ] Confirm declining the reset keeps the connection blocked.
- [ ] Confirm an intentional reset requires a separate second login attempt.
- [ ] Close the host and verify its child processes and listening ports terminate.

## Release hygiene

- [ ] Version numbers agree across `version.h`, `package.json`, both Cargo manifests, lockfiles,
  and `tauri.conf.json`.
- [ ] `README.md`, `BETA.md`, `DISCLAIMER.md`, `SECURITY.md`, and `HARDENING.md` are included.
- [ ] No `.env`, PEM/key files, runtime data, passwords, tokens, recovery codes, or private test
  conversations are committed or packaged.
- [ ] The release is labeled **Beta** and links to `BETA.md` and `DISCLAIMER.md`.
- [ ] SHA-256 hashes are published for distributed archives and binaries.
- [ ] Known failures and skipped tests are stated in the release notes.
