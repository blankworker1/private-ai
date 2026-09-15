# Phase 2 — Pending Updates

Holding pen for decisions and clarifications that surface before Track 2 (`spec-community-ai.md`, `spec-coop-ai.md`, `archiver-protocol-v2.md`, `hardware-relaybox.md`) is actively being worked on. Nothing here has been applied to those documents yet — deliberately held until a real Phase 2 pass, per Carl's instruction not to touch them prematurely. Clear each item out (into the actual doc) once addressed.

---

### 1. Loomio hosting — confirmed same-PC, full suite per node
Node PC runs the entire stack on one machine: Ollama, Open-WebUI, Redis, Gitea, **and Loomio** — not split across separate hardware. Consistent with the existing self-hosting approach (`archiver-protocol-v2.md` §3 already specifies self-hosted Loomio via the official `loomio-deploy` repo, Tailscale-only, not their paid hosted SaaS — that part was already correct). What's new: explicit confirmation it shares the same physical PC as everything else, which isn't currently stated outright anywhere.
**Action when addressed:** add Loomio to `spec-community-ai.md` §6's hosting list (currently lists Shared AI Brain, Redis, `oikb`, Tailscale — no Loomio, since §6 predates the Archiver Protocol v2 Loomio decision).

### 2. Node PC sizing — five concurrent services, not four
Follows directly from #1: hardware sizing for the node PC needs headroom for five always-on services sharing one machine's RAM/disk (Ollama, Open-WebUI, Redis, Gitea, Loomio), not the four currently implied by §6. Loomio itself is lightweight, but it's still another persistent process on the same box.
**Action when addressed:** revisit `spec-community-ai.md` §6's hardware assumption (currently just "same 16GB VRAM starting assumption" — doesn't address system RAM/disk headroom for the full service count, similar to the gap that came up for the Home AI Brain PC's own system RAM before Radiance flagged it).

### 3. Victron MultiPlus-II GX (solar/battery monitoring) — sequenced with Phase 2 solar build
**Confirmed as the first real Shape B test device.** Follows the Shape B pattern defined in `shape-b-integrations.md` §3 — deterministic poll of the MultiPlus-II GX's built-in Venus OS (local Modbus/MQTT, no Victron cloud API needed) → dated markdown snapshot → `oikb` → AI reads. Not buildable until the Phase 2 solar/battery hardware (`hardware-power.md` §4) actually exists.
**Action when addressed:** add as the first concrete Shape B integration once Phase 2 solar hardware is in place; reference `shape-b-integrations.md` rather than re-deriving the pattern in the spec.

---

*v1 — first version, started 2026-09-15.*
