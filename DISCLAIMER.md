# Disclaimer

**Release status: Inner Circle 0.9.0 Beta 1.** Beta means the project is still being validated and
may contain security, correctness, interoperability, data-loss, or availability defects.

This project — the host app (`server/`), the member client (`client-tauri/`), the shared crypto
crate (`crypto-core/`), and the bundled Node.js server (`server/server/`) — was designed and
written almost entirely by an AI assistant (Claude), working from a person's instructions over
many iterative sessions, with the person reviewing and testing along the way but not writing most
of the code by hand.

That has real implications, laid out honestly below rather than left implicit.

## This is not production-grade software

This is a hobby/personal project for running a small, private, self-hosted group chat among people
who trust each other and the person hosting it. It has not been audited by a professional security
researcher, has not been penetration-tested, and has not been reviewed by anyone other than the AI
that wrote it and the person who asked for it. Treat it accordingly:

- Do not use it for anything where a security failure would have serious consequences (regulated
  data, safety-critical communication, anything where the participants' safety depends on the
  encryption actually holding up under a determined, resourced attacker).
- The encryption design (Olm/Megolm via the `vodozemac` crate, plus a custom AES-256-GCM "epoch
  ring" scheme for the newer persistent-history feature) uses well-regarded cryptographic
  primitives and protocols, but the *implementation* gluing them together is unaudited. A
  correctly-chosen algorithm does not guarantee a correct implementation.
- "Server-blind" claims in this project's own planning documents (`PLAN.md`) describe the intended
  design — the server is meant to never see plaintext — but that intent has been verified by the
  same AI-driven testing process that built the feature, not by an independent third party.

## What "verified" means in this project's own documentation

`PLAN.md` uses the phrase "verified live" a lot, and means something specific and honest by it: an
actual test was run — a real server process, a real (simulated) browser or app window, real network
traffic — and its actual output was checked, rather than the code just being read and judged
plausible. That is a meaningfully higher bar than "looks right," and it is the standard this
project has tried to hold consistently. But it is still not the same as:

- A real, adversarial security review.
- Testing on the actual target platform in every case — a fair amount of `client-tauri` verification
  happened on a Linux sandbox standing in for the real target (Windows + WebView2), and the host
  app (`server/`) has never been compiled in any sandbox used so far, only reviewed structurally.
  `PLAN.md` and `HANDOFF.md` both flag exactly which claims fall into this category — look for
  "needs your machine" / "not yet live-exercised" / "worth a click-through on your own machine."
- Long-term, real-world load or abuse testing. Everything has been tested in short, deliberate
  sessions, not months of ordinary use by many people.

## Known, deliberately-scoped-out limitations

A few things were discussed directly with the project's owner and intentionally left undone or
deferred, not overlooked — see `PLAN.md` for the reasoning behind each:

- The old wxWidgets member client has been retired and removed from this delivery — see
  `PLAN.md`'s "phase 12" write-up. `client-tauri` is now the only member client.
- The persistent-history ("forum mode") feature is fully built for the server, the host app
  (circle-creation settings), the reference web client, AND `client-tauri` — reachable from
  either member client now, not just the web client — see `PLAN.md`'s phase-5 write-up and the
  "Post-phase-11 sweep" section right after it (native message removal, join-warning/light, and
  search all landed there too). One real, still-open gap specific to `client-tauri`, not shared
  by the web client: it has no UI anywhere to create a non-public (private/locked) Circle at all
  (one can only be created via a direct API call or the web client today).
- An account-level recovery-code backup now covers the "every device that held a conversation's
  ring is offline/lost at once" scenario described in the paragraph above, on both member clients —
  see `PLAN.md`'s "Critical bug found and fixed: persistent-history messages silently disappearing"
  section. A random, high-entropy one-time recovery code (never derived from the login password,
  shown exactly once at first setup) backs up every epoch ring as an opaque, server-blind blob; a
  device that can't decrypt a conversation with existing history is prompted for that code rather
  than silently fabricating a fresh key or, previously, simply losing access. This meaningfully
  narrows the "no formal backup/recovery scheme" gap that used to apply unconditionally — but it is
  still only as good as the person actually saving that one-time code somewhere safe: losing the
  recovery code AND every device that ever held a room's keys still means that room's history
  (under the default server-blind design) is genuinely, permanently unreadable, by design.

## What to do with this

Read it, test it yourself, and treat any specific security or correctness claim in `PLAN.md` as "an
AI said this was true and checked it the way described" rather than "an independent authority
verified this." If you plan to rely on this for anything beyond casual, low-stakes use among
trusted friends, get an independent, human security review first — particularly of the
cryptographic code paths (`crypto-core/`, `client-tauri/src-tauri/src/crypto/`,
`server/server/public/epoch.js`) and the server's access-control logic
(`server/server/dist/server.js`, `permissions.js`).
