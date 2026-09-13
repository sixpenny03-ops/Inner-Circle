# Hardening pass changelog

Release line: **0.9.0 Beta 1**.

## Changed

- Repaired the clean-checkout Node build contract by treating the shipped `dist/` and `public/`
  JavaScript as canonical source and validating all JavaScript with Node's parser.
- Corrected the Node package entry point and AGPL license metadata.
- Added Content Security Policy, Referrer Policy, and Permissions Policy response headers.
- Extended the security-header regression test for the new policies.
- Qualified server-blind encryption claims with the device safety-number requirement.
- Corrected misleading comments that described silent acceptance of any TLS certificate as
  equivalent to a browser's explicit certificate exception.
- Added `HARDENING.md` with completed work, review requirements, recommended certificate-trust
  designs, and the scope of independent review still needed.
- Added a prominent publication warning: public source review is welcome, while compiled releases
  still require platform testing and review.

## Validation performed

- `npm run build`: passed; 61 canonical JavaScript files parsed successfully.
- `package.json` and `package-lock.json`: parsed successfully.
- Full Node integration tests were not run because dependencies were unavailable in the review
  environment.
- Rust tests and compilation were not run because Cargo was unavailable in the review environment.

## TOFU certificate authentication

- Replaced unconditional certificate acceptance with a persistent SHA-256 fingerprint store keyed
  by exact `host:port`.
- Shared the verifier between native REST and WebSocket connections.
- Added real TLS 1.2/1.3 handshake-signature verification rather than asserting validity.
- Added commands to display and deliberately forget a saved fingerprint.
- Added a certificate-change warning and explicit reset flow to the sign-in UI. A mismatch fails
  closed and never retries automatically.
- Removed the obsolete accept-any-certificate module.
- This Rust change is source-complete but requires a clean Windows compile and end-to-end test
  because Cargo is unavailable in the review environment.
- Added a Windows GitHub Actions workflow that installs locked Node dependencies, runs every
  server test, tests the cryptographic core, and compiles/tests the native client on every push.

## Documentation and release labeling

- Unified the repository, native client, host app, cryptographic crate, and Node server on version
  `0.9.0-beta.1`.
- Added consistent beta labeling, intended-use boundaries, known limitations, contribution rules,
  and a release checklist.
- Made successful Windows CI and manual TLS replacement testing explicit release gates.

## Follow-up correction

The first version of this pass set `script-src 'self'`, which blocked `Olm.init()` from compiling
the bundled WebAssembly encryption module. Because authentication initializes Olm before its HTTP
request, the UI misleadingly displayed “Could not reach the server.” The policy now includes the
narrower CSP source expression `'wasm-unsafe-eval'`, permitting WebAssembly compilation without
permitting general JavaScript `eval`, and the security-header regression test checks for it.
