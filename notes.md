# Phase 2 — Pending Updates

Holding pen for decisions and clarifications that surface before Phase 2 (`spec-community-ai.md`, `spec-coop-ai.md`, `archiver-protocol.md`, `hardware-relaybox.md`) is actively being worked on. Nothing here has been applied to those documents yet — deliberately held until a real Phase 2 pass, per Carl's instruction not to touch them prematurely. Clear each item out (into the actual doc) once addressed.

---

### 1. Loomio hosting — confirmed same-PC, full suite per node
Node PC runs the entire stack on one machine: Ollama, Open-WebUI, Redis, Gitea, **and Loomio** — not split across separate hardware. Consistent with the existing self-hosting approach (`archiver-protocol.md` §3 already specifies self-hosted Loomio via the official `loomio-deploy` repo, Tailscale-only, not their paid hosted SaaS — that part was already correct). What's new: explicit confirmation it shares the same physical PC as everything else, which isn't currently stated outright anywhere.
**Action when addressed:** add Loomio to `spec-community-ai.md` §6's hosting list (currently lists Shared AI Brain, Redis, `oikb`, Tailscale — no Loomio, since §6 predates the Archiver Protocol v2 Loomio decision).

### 2. Node PC sizing — five concurrent services, not four
Follows directly from #1: hardware sizing for the node PC needs headroom for five always-on services sharing one machine's RAM/disk (Ollama, Open-WebUI, Redis, Gitea, Loomio), not the four currently implied by §6. Loomio itself is lightweight, but it's still another persistent process on the same box.
**Action when addressed:** revisit `spec-community-ai.md` §6's hardware assumption (currently just "same 16GB VRAM starting assumption" — doesn't address system RAM/disk headroom for the full service count, similar to the gap that came up for the Home AI Brain PC's own system RAM before Radiance flagged it).

### 3. Victron MultiPlus-II GX (solar/battery monitoring) — sequenced with Phase 2 solar build
**Confirmed as the first real Shape B test device.** Follows the Shape B pattern defined in `data-template.md` §3 — deterministic poll of the MultiPlus-II GX's built-in Venus OS (local Modbus/MQTT, no Victron cloud API needed) → dated markdown snapshot → `oikb` → AI reads. Not buildable until the Phase 2 solar/battery hardware (`hardware-power.md` §4) actually exists.
**Action when addressed:** add as the first concrete Shape B integration once Phase 2 solar hardware is in place; reference `data-template.md` rather than re-deriving the pattern in the spec.

### 4. Personal Layer editor — Obsidian → GitJournal (Track 1 decided, Track 2 docs not yet updated)
Track 1 (`spec-home-ai.md` v19) replaced Obsidian + GitSync + Save as Local Images with GitJournal (one app, native git, native image insertion, genuine backlinks) — see `hardware-tablet.md` §5 for the full reasoning (open source, maintenance profile checked, licensing clarified). Web clipping was already decoupled from the editor in v18, which is what made this swap possible without losing anything.
**Action when addressed:** `spec-community-ai.md` and `spec-coop-ai.md` both describe Archivers/board members using "the same Android-first pattern as the household design" — they'll inherit stale Obsidian references the same way Track 1's own docs did before this pass. Update alongside whatever else Track 2 needs when that phase actually starts, not before.

---

*v1 — first version, started 2026-09-15.*
