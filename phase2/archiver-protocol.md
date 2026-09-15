# The Archiver Protocol

## Identity, Decision-Making, and Enforcement — Now Built Around Loomio

**Applies to:** any 6-person node needing formal decisions before canonicalizing content — the Community AI archive's Archivers, or a Coop AI board. Same mechanism, same reasoning; Loomio doesn't care which context it's used in.

**Companion to:** Community AI spec (Draft v6) and Coop AI spec.

---

## 1. Identity & Enrollment

*(Unchanged from v1)*

- Each Archiver is issued a **hardware security token** (YubiKey-class: FIDO2 + SSH support, USB-C, ideally NFC). The private key never leaves the token — it's generated on the device itself; authentication and signing happen by sending a request to the token and receiving a response, not by ever exposing the raw key.
- **Two tokens per Archiver, not one.** A primary they carry, and a backup stored somewhere physically separate from the primary — the same "don't let one point of failure lose everything" discipline already applied to the local-only git backup remote.
- **Enrollment is in-person, not digital.** The first handoff of a token to an Archiver is the actual root of trust in this whole system — no cryptographic scheme substitutes for someone physically known to the group handing it over.

## 2. Setup Verification (do this first)

- **Verify hardware-key signing on GitSync (Android) before relying on it for anything.** Whether GitSync has implemented USB-C/NFC communication with an external hardware token at all was an open engineering question, not a confirmed capability — get one hardware key, one Android device with GitSync installed, and attempt an actual signed push before assuming this works for all 6 Archivers.
- **Fallback if it doesn't work:** the household spec's own "desktop as secondary option" pattern solves this cleanly — an Archiver does the signed push from a laptop instead, where FIDO2 SSH support is much more mature and well-tested, while still using Android day-to-day for everything else. Not a failure of the design, just routing one specific operation to whichever device actually handles it.

## 3. Governance — Loomio as the Sole Decision Layer

Loomio (self-hosted, open-source, AGPL) replaces the protocol's earlier bespoke voting design. It's worker-cooperative-built, in active use across 100+ countries, purpose-built for exactly this: formal proposals, structured consent voting, threaded discussion, and a permanent decision archive built around ICA Principle 2 (one member, one vote) — the actual cooperative governance principle.

**Self-hosting:** officially supported and documented by the Loomio team itself (an official `loomio-deploy` repository, Docker Compose, step-by-step guide) — not a community workaround. The default deploy guide assumes a public-facing setup (public IP, domain, Let's Encrypt); this project instead runs Loomio privately, reachable only by the 6 Archivers/board members via Tailscale, using Tailscale's own HTTPS certificates for tailnet-only services — the same pattern already used for Open-WebUI.

**How the pieces fit:**
- **Notes still handles live drafting** — Loomio has no live co-editing, so this role doesn't change at all.
- **Loomio replaces Channels' informal discussion role with a formal one** — a proposal gets created (linking to the Notes document), the group votes, and Loomio produces a real, timestamped outcome record.
- **Closing a poll doesn't automatically execute anything** — a human is prompted to write an "Outcome" statement summarizing what happened and what's next. This matches the protocol's existing "not trustless execution, a human still acts" principle, not a limitation of it.
- **The actual push:** once the outcome is recorded as passed, the designated Archiver executes the git push — the commit message references the Loomio poll's ID/URL, giving cleaner traceability than an informal chat link would.
- **Integration into the knowledge base:** Loomio exposes proposal/poll creation and results over a real, documented API (`api/b1/polls`). The decision record is pulled into `oikb` as another feed, the same way the Network Library feeds each node — so the AI can be queried about governance history conversationally, not just archive content.
- **Everyday toolset:** Obsidian (personal notes) + Open-WebUI (AI chat/Notes/Channels) + Loomio (formal decisions) — three separate surfaces, no merged app. Loomio runs as an installable web app via "Add to Home Screen," the same pattern as Open-WebUI; real browser push notifications work, so an Archiver is notified when a vote needs attention. (Loomio's team is building native iOS/Android apps as a next step — not yet the default; the PWA is the current, reliable answer.)

## 4. Git-Level Enforcement — Simplified to Identity Only

With Loomio actually providing the vote, keeping *both* a full Loomio poll and a separate N-of-6 git approval gate would double-gate the same decision twice. This is dropped:

- **Git-level requirement: pusher must be a verified Archiver (identity check only, via the SSH-capable hardware token).** No separate approval-count threshold on the git side.
- **Commits are SSH-signed** using the same hardware token used for authentication — one credential, two jobs, no separate GPG setup.
- **Commit messages reference the originating Loomio poll** (ID/URL) — without this, git has no way of knowing which decision a given push corresponds to.

## 5. What This Deliberately Is Not

- **Not a blockchain.** No consensus mechanism, no mining, no distributed trust-minimization — the Archivers/board already know and trust each other, and the self-hosted Gitea server is trusted infrastructure the group controls. Enforcement is a policy your server checks, not a cryptographic guarantee independent of anyone's goodwill.
- **Not a token-weighted DAO.** Six named, permissioned, equal-standing Archivers/board members — one member, one vote. Closer in spirit to a cooperative's own bylaws than to crypto-DAO governance.
- **Not trustless execution.** A human still runs the actual push once the Loomio outcome is recorded; nothing executes itself automatically without a person acting.
- **`gittuf`** (genuine cryptographic M-of-N ref-signing, independent of any server) remains a documented future option if the group's trust model ever changes — not needed at this scale today.

---

*Draft v2 — supersedes v1. Loomio adopted as the sole governance/voting layer, replacing the bespoke voting design; git-level enforcement simplified from an N-of-6 approval count down to identity verification only, since Loomio's poll is now the sole source of the actual governance decision. [date: 2026-09-14]*
