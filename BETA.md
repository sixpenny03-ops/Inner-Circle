# Beta status and known limitations

Current release line: **0.9.0 Beta 1**.

## What “beta” means here

- The project is feature-rich enough for hands-on testing and public source review.
- Clean Windows builds and automated tests are required on every change.
- Interfaces, stored data, recovery behavior, and installation steps may change without backward
  compatibility during the beta period.
- Users should keep backups of recovery codes and circle data and expect defects.
- Beta does not mean independently audited, production-supported, or suitable for high-risk data.

## Intended beta use

- Small, private circles of people who know and trust one another.
- Development, interoperability testing, usability testing, and security review.
- Low-stakes conversations where temporary downtime or data loss is tolerable.

## Not intended for

- Regulated, medical, financial, legal, journalistic-source, safety-critical, or emergency use.
- Situations where a participant’s safety depends on anonymity or message confidentiality.
- Multi-tenant hosting or exposure to untrusted public users.

## Current security boundaries

- The native client uses trust on first use. It detects later certificate replacement but cannot
  detect an attacker already present during the first connection.
- Participants must compare device safety numbers through a separate trusted channel to defend
  against server-side identity-key substitution.
- The custom persistent-history epoch-ring protocol has not received an independent cryptographic
  review.
- A lost recovery code plus loss of every device holding the relevant keys can make encrypted
  history permanently unrecoverable by design.

## Current validation status

- Canonical server JavaScript passes deterministic syntax validation.
- GitHub Actions is configured to install locked dependencies and run server and Rust tests on
  Windows for every push and pull request.
- The TOFU source requires its first successful Windows CI run and a manual certificate-change
  exercise before a beta binary is published.
- No independent professional security audit or long-duration load test has been completed.

See [`HARDENING.md`](HARDENING.md), [`SECURITY.md`](SECURITY.md), and
[`RELEASE-CHECKLIST.md`](RELEASE-CHECKLIST.md).
