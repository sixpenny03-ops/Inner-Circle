# Security hardening status

This document distinguishes completed safeguards from work that still requires review.

## Completed in this pass

- The Node package identifies the shipped JavaScript as canonical source and validates it from a
  clean checkout.
- Node package licensing matches the repository's AGPL-3.0-only license.
- Browser responses include Content Security Policy, referrer policy, permissions policy,
  clickjacking protection, and MIME-sniffing protection.
- The browser policy permits the bundled Olm WebAssembly encryption module.
- Public documentation states the device-key verification requirement and TLS trust model.

## Native certificate authentication

The native client uses persistent trust on first use (TOFU) for the host's generated self-signed
certificate. The first SHA-256 certificate fingerprint seen for an exact `host:port` is saved in
the app-data directory. REST and WebSocket connections share the same trust store and reject every
later mismatch.

If a host intentionally replaces its certificate, the sign-in UI displays the expected and
presented fingerprints, warns about possible impersonation, and offers a deliberate reset. It
never retries with the new certificate automatically; the user must verify the address and press
Log in again.

TOFU cannot detect an attacker already intercepting the very first connection. Higher-assurance
options include a publicly trusted certificate or transferring the initial fingerprint through an
authenticated invite.

The implementation requires a clean Windows compile and end-to-end exercise before distributing a
binary containing it.

## End-to-end identity qualification

Olm/Megolm encrypt message content, but the circle server distributes device identity keys. Until
participants compare safety numbers through an authenticated channel, a malicious or compromised
server may substitute a device identity during initial key discovery. Server-blind confidentiality
against an active server therefore depends on identity verification.

## Independent work still required

- Independent review of authentication, authorization, device lifecycle, key distribution, and
  the custom epoch-ring history protocol.
- Adversarial tests for first-connection interception, certificate replacement, replay, rollback,
  and concurrent membership/key-rotation races.
- Clean Windows builds and end-to-end tests using the actual WebView2 and wxWidgets applications.
- Gradual decomposition of the largest JavaScript and C++ translation units.
