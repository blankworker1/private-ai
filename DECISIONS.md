# Decisions

A scannable index of every major decision made on this project, in one place. Each entry is deliberately one line — the full reasoning lives in the linked document, this file exists so a newcomer doesn't have to read every file's version history to find out *what* was decided and *why*, briefly.

Format: **Decision** — one-line rationale. `source doc §section`

---

## Architecture

- **Three-layer design (Personal / Node / Network)** — private per-person layer, deliberately-curated shared library, one local AI serving everyone; privacy by construction, not policy. `spec-home-ai.md` §1
- **One-at-a-time GPU processing** (`OLLAMA_NUM_PARALLEL=1`) — optimizes for one person getting full processing power over splitting the GPU across simultaneous users. `spec-home-ai.md` §6
- **Two-phase project structure** (Phase 1 Home AI, Phase 2 Community/Coop AI) — validate the core design on one household before committing to multi-party governance complexity. `README.md`
- **No task-scheduling AI agent, no Umbrel merge** — keeps the Brain PC single-purpose; live AI tool-access into other devices would be the first thing in the whole architecture to break "not trustless execution, a human still acts." Discussed, not written into a spec file — rationale lives in project history only.
- **Web clipping decoupled from the note editor** — a Shape B pattern instance (ArchiveBox/Wallabag on the Brain PC) rather than an app plugin; this is what later made the editor swap to GitJournal possible without losing anything. `spec-home-ai.md` §5, `phase2/data-template.md` §4a
- **Two named network modes: Local and Remote** — the core AI-and-knowledge loop has zero inherent internet dependency once a model is loaded; naming this explicitly rather than leaving it implicit. Local Mode has no real structural user-count limit (the GPU queue is the only shared constraint, and it degrades gracefully). The "6 users" hard cap is a Tailscale free-tier pricing artifact, not an architectural one — Headscale (self-hosted, open source) removes it entirely, not yet adopted. The household's 6-seat number itself stays, re-justified as a deliberate UX/data-sharing/security decision independent of the coordination layer. `spec-home-ai.md` §7/§7a

## Hardware — Phase 1

- **Supplier: Radiance Systems** (CoreAI 16, RTX 5060 Ti 16GB) + Turnkey AI Pack — pre-tested, burn-in tested, full household-specific configuration confirmed at no extra cost. `hardware-brain-pc.md` §3–§5a
- **Declined RAM upgrade** (stayed at base 16GB) — €726 for the upgrade, no expectation of genuinely simultaneous heavy use across all five accounts. `hardware-brain-pc.md` §6a
- **Declined storage upgrade** (stayed at base 1TB) — git-based markdown libraries are small; no real constraint. `hardware-brain-pc.md` §6a
- **Personal Layer trial: five existing/random Android devices, not new purchases** — a stronger real-world test of the "any Android device works" compatibility claim than five matched new units. `hardware-tablet.md` §4
- **Personal Layer editor: GitJournal**, replacing Obsidian + GitSync + Save as Local Images — native git sync, native image insertion, genuine backlinks, one app instead of three; only possible once clipping moved out of the editor. `hardware-tablet.md` §5
- **Shared Library backup: rotated, air-gapped external drive**, script-based overdue reminder (not AI judgment) — closes the off-site redundancy gap without a cloud dependency. `spec-home-ai.md` §4a
- **Phase 1 UPS: 1500VA/1000W pure sine wave, ~30 min runtime, ≤€250** — pure sine wave required for Active PFC PSU compatibility; comfortable headroom over peak load. `hardware-power.md` §3
- **Phase 2 power deferred: off-grid solar + battery**, sized for a 24-hour outage — a separate, much larger purchase (~€2,500–4,500), not bundled into the Phase 1 UPS budget. `hardware-power.md` §4

## Software & Data

- **Shape B pattern for any passive data integration** (sensor, web archive, anything) — deterministic script polls, writes a snapshot, `oikb` ingests, AI only ever reads; never live AI tool-access. `phase2/data-template.md`
- **Image captions required for AI-queryability** — Open-WebUI's knowledge base only processes text; an uncaptioned image is invisible to the AI regardless of which app saved it. `phase2/archiver-protocol.md` §5
- **Governance: Loomio**, replacing an earlier bespoke voting design — mature, purpose-built cooperative governance software; self-hosted via the official `loomio-deploy` repo, not their paid SaaS. `phase2/archiver-protocol.md` §3
- **Network Library replication: Radicle** (peer-to-peer git), not a shared GitHub org — keeps all federation data on member-owned hardware. `phase2/spec-community-ai.md` §7
- **Git server: Forgejo, not Gitea** — Gitea's trademark and governance moved to a for-profit company (Gitea Ltd); Forgejo is community-governed under Codeberg e.V. (non-profit), GPL-licensed, and building forge federation that aligns with the project's own federated design. Concrete evidence, not just values alignment: Gitea is "open core" (some features not released under a free license) as a direct consequence of the governance shift. Switched while switching cost was near-zero — nothing built yet. `README.md` (Open Source table), `phase2/spec-community-ai.md`, `phase2/archiver-protocol.md`, `phase2/spec-coop-ai.md`, `phase2/network-library-public-mirror-overview.md`

## Legal & Project Identity

- **License: AGPL-3.0** for private-ai's own content — specifically closes the loophole of someone running this as a closed SaaS, the exact failure mode the project exists to opt out of. `LICENSE.md`
- **Logo: tilde mark, RGB-coded to the three architecture layers** (red/personal, green/node, blue/network) — chosen for the "home directory" association and because it could be rendered as flat color-block segments.
- **Folder structure: `/phase2` subfolder** for all Community AI / Coop AI / relay box content — keeps Phase 2 scope physically separate from active Phase 1 work.

---

*v1 — first version of this log, compiled retrospectively from the working history. Add a new entry here whenever a decision this significant gets made — don't wait for a full doc rewrite to record it. [date: 2026-09-16]*
