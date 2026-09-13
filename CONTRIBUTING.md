# Contributing

Inner Circle is beta software with security-sensitive code. Small, reviewable changes with tests
are preferred.

1. Create a branch from `main`.
2. Do not commit secrets, runtime circle data, certificates, recovery codes, or real conversations.
3. Run the relevant Node and Rust tests.
4. Explain security and compatibility effects in the pull request.
5. Wait for the Windows workflow to pass before merging.

Report suspected vulnerabilities privately according to [`SECURITY.md`](SECURITY.md), not in a
public issue. Cryptographic or authentication changes should include adversarial tests and should
not be described as audited unless an independent qualified reviewer has actually audited them.
