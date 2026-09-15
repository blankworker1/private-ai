# private-ai

Self-sovereign, self-hosted AI architecture — one underlying design (Personal Layer → Node/Shared Library → Shared AI Brain, Ollama + Open-WebUI) applied across three use cases, plus a public-facing extension.

## Documents

| File | What it is |
|---|---|
| `local-ai-spec-draft-v14.md` | **Household AI** — private, 5-person family platform. Android-first Personal Layer + shared Ollama/Open-WebUI "Brain." |
| `implementation-checklist.md` | Companion to the household spec — 17 open build/decision items, execution detail kept separate from the pitch. |
| `hardware-brain-pc.md` | Live decision record for the shared Brain AI PC — supplier (Radiance Systems), model, Turnkey Pack, cost tally, setup sequence. |
| `hardware-tablet.md` | Live decision record for the five Personal Layer devices — requirements, selection criteria, per-device setup sequence. |
| `community-ai-spec-v6.md` | **Community AI** — 6-person "Archiver" board building a collaboratively-curated digital archive. Introduces the Node Library (private to one node) and Network Library (shared across federated nodes via Radicle). |
| `coop-ai-spec.md` | **Coop AI** — same architecture, aimed at formal cooperative governance: board resolutions and records via Loomio, made AI-queryable. |
| `archiver-protocol-v2.md` | **Governance mechanism** shared by Community AI and Coop AI — hardware-token identity, Loomio as the vote/decision layer, git-level enforcement simplified to identity verification. |
| `network-library-public-mirror-overview.md` | The one deliberate exception to "no open ports" — a public, browsable/searchable mirror of the Network Library. No AI assistant on the public side; visitors bring their own. |

## How they relate

```
Household AI ─┐
              ├─ same architecture (git-based storage, oikb ingestion, Tailscale access)
Community AI ─┤
              │     └─ Archiver Protocol (identity + Loomio governance)
Coop AI ──────┘

Community AI's Network Library ─── Network Library Public Mirror
  (federation-private)               (public exception)
```

## Status

All specs current as of 2026-09-14. Version numbers in each filename/header reflect that document's own draft history; not all documents are at the same maturity — Coop AI is v1, Household AI is v13.

## Governing principle throughout

No open ports, nothing exposed to the public internet, private Tailscale network only — held everywhere except the Network Library Public Mirror, which is the one deliberate, separately-scoped exception.
