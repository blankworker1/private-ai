# private-ai

**A household that owns its own AI, instead of renting one.**

Five people, one shared brain, zero cloud dependency. private-ai is a self-hosted AI platform built around a simple premise: the same conversational AI experience people already pay $20/month per person for, running instead on hardware the household owns outright — with a knowledge base built from what the household deliberately chooses to teach it, not from silently ingesting everyone's private notes.

The same underlying architecture — a private per-person layer, a deliberately-curated shared knowledge base, one local AI brain serving everyone — extends beyond the household to two further use cases: a community association archive, and a cooperative's formal governance record. One design, three contexts.

## Why this, not a subscription

- **No recurring cost.** One hardware purchase, not $100+/month indefinitely for five separate subscriptions.
- **Privacy by construction, not by policy.** Personal notes are never ingested by the AI — only what's deliberately pushed to a shared library ever becomes part of what the AI knows. No provider terms of service to trust, because there's no provider.
- **Immune to the failure mode that ruined shared broadband.** A fixed, ACL-enforced account count means there's no path for the customer count to quietly grow past the hardware — because there's no one selling seats.
- **Backup as a side effect of daily use, not a chore.** The same git-sync habit that keeps a vault current across a phone and a tablet produces a tested backup for free — no separate discipline required, and for the shared library, AI access itself is the incentive: no push, no backup, no AI.
- **No open ports, ever** — remote access is a private Tailscale network throughout. The one deliberate, separately-scoped exception is a public mirror of already-decided-to-be-public archive content, with no AI assistant on the public side.

## Project structure — two tracks

**Track 1 — Home AI (active).** A private AI platform for a 5-person household. This is where real hardware decisions, real suppliers, and real setup work are happening right now — see Status below.

**Track 2 — Community AI & Coop AI (deferred).** The same architecture applied to a community association's shared archive, and to a cooperative's formal governance record. Deliberately not started yet — Track 2 waits until Track 1's real-world trial has actually validated the core design.

*(Note: the Home AI spec itself has its own internal "Phase 1 / Phase 2" — Phase 1 being single-user validation, Phase 2 being full household rollout. That's a nested stage split within Track 1, not the same thing as the Track 1/Track 2 split above.)*

## Documents

| File | What it is |
|---|---|
| `spec-home-ai.md` | **Home AI** — private, 5-person household platform. Android-first Personal Layer + shared Ollama/Open-WebUI "Brain." Currently Draft v15. |
| `implementation-checklist.md` | Companion to the Home AI spec — 17 open build/decision items, execution detail kept separate from the pitch. |
| `hardware-brain-pc.md` | Live decision record for the shared Brain AI PC — supplier (Radiance Systems), model, Turnkey Pack, cost tally, setup sequence. |
| `hardware-tablet.md` | Live decision record for the five Personal Layer devices — requirements, selection criteria, per-device setup sequence. |
| `hardware-power.md` | Live decision record for backup power — Phase 1 UPS (decided) and Phase 2 off-grid solar + battery (deferred, sized for a 24-hour outage). |
| `spec-community-ai.md` | **Community AI** — 6-person "Archiver" board building a collaboratively-curated digital archive. Introduces the Node Library (private to one node) and Network Library (shared across federated nodes via Radicle). |
| `spec-coop-ai.md` | **Coop AI** — same architecture, aimed at formal cooperative governance: board resolutions and records via Loomio, made AI-queryable. |
| `archiver-protocol-v2.md` | **Governance mechanism** shared by Community AI and Coop AI — hardware-token identity, Loomio as the vote/decision layer, git-level enforcement simplified to identity verification. |
| `hardware-relaybox.md` | Placeholder for the Track 2 relay box hardware decision — not started, deferred until Track 1 validates. |
| `network-library-public-mirror-overview.md` | The one deliberate exception to "no open ports" — a public, browsable/searchable mirror of the Network Library. No AI assistant on the public side; visitors bring their own. |

## How they relate

```
Home AI ──────┐
              ├─ same architecture (git-based storage, oikb ingestion, Tailscale access)
Community AI ─┤
              │     └─ Archiver Protocol (identity + Loomio governance)
Coop AI ──────┘

Community AI's Network Library ─── Network Library Public Mirror
  (federation-private)               (public exception)
```

## Status

**Track 1 — Home AI:** spec at Draft v15. Hardware finalized with Radiance Systems — CoreAI 16 (RTX 5060 Ti 16GB) with their Turnkey AI Pack, full custom configuration confirmed at no extra cost, quoted at €2,362.31 (VAT included, Italy delivery). Order confirmation sent; awaiting deposit details. Personal Layer trial will run on five existing/random Android devices (not new purchases), a deliberate stress test of the spec's device-compatibility claim. Next concrete action, independent of the order: run the checklist's backup restore test (item 1).

**Track 2 — Community AI, Coop AI:** not started. Deferred by design until Track 1's real-world trial validates the core approach.

Version numbers in each spec's own header/footer reflect that document's individual draft history — not all documents are at the same maturity; Coop AI is v1, Home AI is v15.

## Governing principle throughout

No open ports, nothing exposed to the public internet, private Tailscale network only — held everywhere except the Network Library Public Mirror, which is the one deliberate, separately-scoped exception.
