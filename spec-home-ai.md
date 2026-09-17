# Home AI Workspace — Specifications 

## A Three-Layer, Self-Sovereign, Multi-User Household Platform

**Companion documents:** [implementation-checklist.md](implementation-checklist.md) — all open execution items and decisions live there, not here. [hardware-brain-pc.md](hardware-brain-pc.md), [hardware-tablet.md](hardware-tablet.md), and [hardware-power.md](hardware-power.md) — live hardware procurement records for the Brain PC, the five 🔴 Personal Layer devices, and backup power.

---

## Executive Summary — Trial Scope, Success Criteria, Cost, Risks

*This section is the pitch. Sections 1–9 below are the detailed architecture backing it up.*

**What this is:** a private AI platform for a 5-person household — the same thin-client experience as a cloud AI service (Claude, ChatGPT), but running entirely on hardware the household owns, with a shared knowledge base built from deliberately-contributed research rather than automatic ingestion of anyone's private notes.

**Proposed trial scope - two steps:**
- **Step 1 (single-user validation):** Buy the GPU, build the Shared AI Brain (Ollama + Open-WebUI), connect via Tailscale, run one person's full stack — 🔴 Personal Layer, Shared Library with at least a few real contributions, AI answering questions grounded in it. Duration: proposed 2–3 weeks.
- **Step 2 (household rollout):** Onboard the remaining four accounts, validate the one-at-a-time GPU queue under real simultaneous demand. Only begun once Step 1's success criteria are met. The status widget is a non-critical nice-to-have for managing remote users' expectations — safe to defer past Step 2 if time is short; the Shared Library sync script, by contrast, is load-bearing and needs to be solid before Step 1 even starts (see risks below).

**Proposed success criteria for Step 1**
- Chat responses return in a reasonable time on real single-file/everyday tasks (benchmark: comparable to the Claude-Sonnet-tie result cited earlier for this hardware tier).
- Shared Library sync runs end-to-end at least once without manual intervention beyond the initial script setup.
- A backup restore has been performed successfully at least once (see companion checklist — this can be tested before any hardware purchase).
- Tailscale + ACL access confirmed working from outside the home network.
- 🔴 Personal Layer (GitJournal) confirmed fully functional with the Brain offline/unreachable, proving the independence claim in practice, not just on paper.

**Cost — the fair comparison isn't a shared commercial login.** A single account shared across 5 people either breaches most consumer AI providers' terms of service, or mixes everyone's conversations together — which defeats the personal-separation this spec is built around. The honest alternative is **5 individual subscriptions**, currently ~$20/user/month for a comparable tier (Claude Pro or ChatGPT Plus, Sept 2026 pricing) — roughly **$100/month, ~$1,200/year, indefinitely, for the household.** At that rate, this platform's one-time €1,400–1,800 is recovered in well under two years, with every year after that being pure savings — before even counting the privacy difference.

**Structural point, not just financial:** this platform isn't immune to sharing — five people genuinely do queue behind one GPU (§4) — but it's immune to the specific *failure mode* that degraded broadband and now shapes commercial AI limits: there's no path by which the customer count grows while the hardware doesn't, because there's no one selling seats. Five named accounts, enforced by the Tailscale ACL, with a free-tier ceiling that makes a 6th person an active, visible decision rather than something that happens by drift. The contention here is fixed and internal, not a growing denominator controlled by someone else's growth incentive.

**Known, accepted risks:** the platform is meaningfully less capable than frontier commercial models on complex, multi-step reasoning; only one person can be actively generating a response at a time, by design; there's a genuine two-app UX cost (GitJournal for notes, Open-WebUI for chat — no seamless single app); the household's own hand-built glue code (Shared Library sync, status widget) has no vendor to call if it breaks silently; and there's no hardware failover if the single PC fails.

---

## 1. Architecture Overview

| | 🔴 Personal Layer | Shared Library | Shared AI Brain |
|---|---|---|---|
| **What it is** | Each person's private GitJournal vault | A shared git repo, household-visible | Ollama + Open-WebUI |
| **Primary device** | **Android tablet/phone** (PC optional, secondary) | The AI PC | The AI PC |
| **GPU needed?** | No, on either platform | No | Yes — the one hardware purchase |
| **Does the AI read it?** | No — never | Yes — this is what feeds the AI's answers | N/A |

**Why Android-first:** no GPU is required for anything in the 🔴 Personal Layer, and it's the device most household members already carry — no dedicated hardware purchase needed per person. A PC remains available as a second option for anyone who wants a larger screen for heavier writing sessions, syncing to the *same* vault rather than a separate one.

**Interaction model — two separate surfaces, no in-app trigger:**
- **GitJournal** — the markdown notebook/document creator. Plain text, always local, works regardless of network or server status.
- **Open-WebUI** — text in, text out, same shape as a Claude conversation, reachable as a separate app/browser tab (not a plugin inside GitJournal). Can be grounded in personal uploads or in the household's Shared Library. Moving anything between the two is manual copy-paste, by design — no plugin bridge, so personal notes never depend on server uptime and never risk leaking into anything shared.

---

## 2. 🔴 Personal Layer — Android (Primary)

- **GitJournal (Android app)** — markdown notes with git sync built in natively, replacing the previous three-app stack (Obsidian + GitSync + Save as Local Images) with one. Open source (AGPL-3.0/Apache-2.0 mix). Auto-commits on every change, auto-pushes by default (configurable). Wiki-style `[[links]]` with genuine backlinks. Images insert directly into the repo, no separate plugin needed. No GPU needed. No AI wired in, deliberately.
- **File conventions:** plain markdown, one topic per file, dated descriptive filenames, YAML frontmatter for tags/date/project, flat folder structure.
- **Maintenance note, worth carrying forward rather than assuming settled:** small-team open-source project, real but modest commit activity (confirmed active as of May 2026), open issues sometimes sit for a while unresolved. Acceptable risk given the vault itself stays plain markdown + git regardless of app — switching editors later costs re-onboarding, not data loss.
- **Web clipping — not a Personal Layer app requirement.** A decoupled service (ArchiveBox/Wallabag) running on the Brain PC, fed by the phone's normal share sheet rather than tied to whichever note editor is installed — see §5 and `data-template.md` §4a for the full mechanism. This is also what freed the editor choice from needing a built-in clipper as a requirement in the first place.
- **Tailscale (remote access)** — official Android app.
- **Optional local AI fallback** — deliberately not included; a small model could run offline on a laptop's CPU/integrated graphics, but this isn't required.

## 3. 🔴 Personal Layer — Desktop/PC (Secondary Option)

Pointed at the same vault via git: **GitJournal (desktop)** — same single app, same native git sync, no separate CLI/plugin needed. Web clipping handled the same decoupled way as §2, device-agnostic. Fully functional, not required for daily use.

---

## 4. Shared Library — Deliberately Contributed, Household-Visible

Bare git repo on the AI PC. Each person clones it, clips into their own subfolder to avoid merge conflicts. **This is the sole source feeding the AI's knowledge** — personal vaults are never ingested.

**Sync mechanism:** Open-WebUI's official **`oikb`** tool, replacing an earlier custom sync-script design — removes the single largest operational risk in that earlier approach (a hand-built script calling the API directly, which could break silently on an Open-WebUI update).

**Governance:** who decides what belongs in it — see companion checklist.

---

## 4a. Shared Library Backup — Air-Gapped, Rotated

Unlike the 🔴 Personal Layer (§2, dual remotes: GitHub + local-only), the Shared Library's canonical copy lives only on the Brain PC (§4) — no separate off-site remote. This closes that gap without adding any cloud dependency or open port, consistent with the project's principle throughout.

**Mechanism — external hard drive, connected only during backup:**
- A backup drive is connected to the Brain PC only for the duration of each backup, then disconnected. Deliberately air-gapped, not a permanently-attached remote — nothing that goes wrong on the Brain PC between backups (corruption, a bad update, anything else) can reach the drive while it's disconnected.
- **Rotation for geographic redundancy:** two drives, alternated. One is always physically off-site (a relative's house, a workplace — anywhere separate from the Brain PC's location) while the other is in use or awaiting its next connection. Without rotation, a single on-site drive protects against device failure but not fire/theft/flood at the one location — the same gap the drive was meant to close.
- **Reminder mechanism — a script, not the AI's judgment:** a scheduled check (same `cron`/Task Scheduler pattern as `oikb`'s sync — §4) reads the timestamp of the last completed backup and, once it's overdue, sends a Tailscale-reachable notification. The trigger is a deterministic timestamp comparison — reliable and auditable — not the LLM independently deciding when to remind. Whether the resulting notification is surfaced as a message inside Open-WebUI or as a plain system notification is a presentation choice on top of that same reliable trigger.
- **Still open:** the backup interval and the rotation interval (need not be the same — e.g. weekly backup, monthly off-site swap) — see companion checklist.

**Resulting redundancy, tallied:** the Brain PC's live copy + the rotated external drive (at least one location always off-site) + the informal redundancy of every household member's own Shared Library clone on their own device (§4) — three real copies, genuine geographic separation, zero cloud dependency.

**Why this design over a cloud remote:** it preserves human-in-the-loop control (someone physically connects the drive and carries it) and keeps the "no data leaves the household's own hardware" principle intact, at the cost of the backup being only as current as the last rotation rather than continuous.

---

## 5. Shared AI Brain — Server

**Actual Phase 1 hardware selected and ordered — see `hardware-brain-pc.md` for the live decision record (supplier, exact model, finalized configuration, cost).** The illustrative build below is kept as the general planning reference — useful for a future Phase 2/Community AI node or any DIY build — not as current pricing.

**Hardware:** one PC, 16GB VRAM tier (e.g. RTX 4070 Ti Super). Doesn't need to scale with household size — because only one job runs on the GPU at a time (§6), five people share the same single-user hardware spec costed for one.

**Realistic EU cost (Sept 2026, inflated by an ongoing global DRAM shortage — treat as elevated, not baseline):**

| Component | Cost |
|---|---|
| RTX 4070 Ti Super (16GB) | ~€850–900 |
| Full system | ~€1,400–1,800 |

**Software (free, open-source):**
- **Ollama** — model engine, background API.
- **Open-WebUI** — multi-user browser front-end. Each household member gets an individual login, isolated chat history, and personal document/knowledge-base uploads.
- **Web archiving service (ArchiveBox or Wallabag)** — replaces the Mobile Webviewer plugin's clipping role, decoupled from the Personal Layer editor entirely (§2/§3). Fed by any device's share sheet; writes dated markdown into the Shared Library, same as any other content. Full mechanism documented as a Shape B pattern instance in `data-template.md` §4a.
- No GitJournal on this layer — Open-WebUI is chat-first, not a writing/notes tool (that's the 🔴 Personal Layer's job).

---

## 6. Multi-User Processing Model

**Design principle:** optimize for one person at a time getting full processing power, not for splitting the GPU across simultaneous users.

Three Ollama settings implement this:
- `OLLAMA_NUM_PARALLEL=1` — one generation at a time gets 100% of the GPU.
- `OLLAMA_MAX_LOADED_MODELS=2` — room for chat model + embedding model, so Shared Library ingestion doesn't queue behind someone's chat.
- `OLLAMA_KEEP_ALIVE=-1` — model stays resident all day; no cold-start delay between household members' sessions.

**What this means day to day:**
- Browsing your own chats, searching documents, organizing — pure database reads, never touches the GPU, always instant regardless of what else is running.
- Submitting a prompt either runs immediately (GPU free) or queues invisibly and just takes a bit longer (GPU busy) — no visible queue position, no accounts/permissions to manage beyond normal login.

---

## 7. Remote Access — Tailscale

- **Free "Personal" plan:** up to 6 users, unlimited devices per user — covers a 5-person household with one slot to spare, at no cost.
- **The 6-seat number is a deliberate UX/data-sharing/security decision, not just a vendor-tier coincidence.** A small, fixed, named group is easier to hold accountable for what gets pushed to the Shared Library, easier to onboard without confusion, and keeps the "who can see what" model simple enough that nobody needs to think about it. That it also happens to fit Tailscale's free tier is convenient, not the actual justification — see §7a for what changes (and what doesn't) if the underlying coordination layer changes.
- **Hard limit to respect, for now:** a 6th unrelated person joining the tailnet moves the *entire* tailnet to paid, per-seat billing, retroactively for everyone. Keep this tailnet to the household only.
- **ACL** restricts the AI server to the named household accounts specifically — this is the actual "seat list" mechanism, not a numeric cap.
- **Tailscale has a mature Android app** — remote access from a tablet works identically to a PC.

**Two-layer marshalling (why remote and local users feel different):**
1. **First-order — Tailscale ACL:** binary, always-on gate. "Are you even allowed to reach this machine." Blind to load.
2. **Second-order — Ollama's queue (§6):** arbitration after you're through the gate. "Whose turn is it." Blind to local vs. remote, and blind to device type.

They don't communicate — a known, unaddressed UX gap for remote users (addressed by the optional status widget — see companion checklist).

---

## 7a. Local vs. Remote — Where the Real Structural Limits Are

Two genuinely different modes, worth naming explicitly rather than leaving implicit — and worth being precise about where each mode's actual limits come from, since it's not where the current 6-seat language might suggest.

**Local Mode — Brain PC and devices on the same physical network.** Zero internet dependency for anything: local inference (Ollama), local retrieval (`oikb` against a local git repo) — the core AI-and-knowledge loop has no inherent internet requirement once a model is loaded. Devices on the same LAN reach Open-WebUI directly — no VPN, no coordination layer, nothing to route through. Tailscale itself already prefers a direct LAN path over its coordination layer when one's available, so a household member at home was never really depending on the coordination server in the first place.

**Local Mode has no real structural user-count limit at any scale this project would reach:**
- Open-WebUI's own accounts are just database rows — no hard cap in the self-hosted version, designed for multi-user deployments well beyond household scale.
- The home LAN itself handles dozens to hundreds of devices without strain — a non-issue at any number this project cares about.
- **The one shared constraint is the GPU queue (§6)** — and it's graceful degradation, not a wall. More people sharing one GPU means a longer queue during simultaneous demand, not a failure. Nothing breaks at a higher number; it just gets slower to wait a turn.

**Remote Mode — reaching the household from outside the physical network.** This is the one case that structurally needs *some* coordination layer — reaching a device behind NAT from outside requires a rendezvous point; that's a fundamental networking constraint, not a Tailscale-specific limitation.

**Here's the part worth being precise about: the "6 users" hard cap in §7 isn't a property of the architecture at all — it's an artifact of one specific vendor choice.** Tailscale's free "Personal" plan caps at 6 accounts as a *pricing-tier* decision, not a technical one. Two ways to provide the coordination layer Remote Mode needs:
- **Vendor Tailscale** (current default, §7) — convenient, zero infrastructure to run, but both the proprietary coordination server *and* its free-tier account cap are Tailscale's business decisions, not architectural limits.
- **Self-hosted Headscale** — Tailscale's protocol, fully compatible with the official client apps, open source (BSD-3-Clause), actively maintained, explicitly acknowledged and sponsored by Tailscale itself for infrastructure-sovereignty use cases. **Removes the 6-user hard cap entirely** — a self-hosted coordination server doing lightweight key-exchange bookkeeping comfortably handles far more registered devices than this project would ever need, on modest hardware. Headscale's own docs describe it as scoped for "personal use or a small organization," but that's stated design intent, not an enforced limit. Needs its own small, always-reachable HTTPS endpoint — best run on separate minimal infrastructure (same pattern as the Network Library Public Mirror, `phase2/network-library-public-mirror-overview.md`) rather than making the Brain PC itself network-facing. **Decision: hybrid, by phase.** Phase 1 (Home AI) stays on vendor Tailscale — the household's 5 users sit comfortably inside the free 6-user cap, so adopting Headscale here would mean taking on new self-hosted infrastructure (still shipping as beta point-releases, community-maintained rather than production-supported) to solve a constraint Phase 1 doesn't actually have. Phase 2 (Community AI / Coop AI) adopts Headscale — that's where the free cap becomes a real constraint on a larger, federated membership, and where the governance case for not letting a vendor gate community access actually applies. The "run it on separate infrastructure, not the Brain PC" guidance above holds regardless of phase; for Phase 2 specifically, that also keeps a Node AI outage and a network-access outage independently diagnosable rather than compounding each other. See phase2/spec-community-ai.md and phase2/spec-coop-ai.md for where this gets built out.

**Net effect, stated plainly:** Local and Remote both funnel into the same shared resource — the one GPU and its queue — and that's the only limit either mode actually has architecturally. The 6-seat number stays as a deliberate household-scale decision (§7), independent of whichever coordination layer is providing Remote access; adopting Headscale would remove the *vendor's* ceiling without changing the household's own reasoning for keeping the group small.

**Web archiving — Local Mode alternative.** The current design (§5) runs ArchiveBox/Wallabag on the Brain PC, which needs the Brain PC to have internet reach. A zero-server alternative already works today with no new tooling: a phone's browser reader mode, manually pasted into GitJournal, pushed via the normal discuss-then-push discipline. This moves the internet dependency to whichever device is doing the fetching, for that moment only — the Brain PC is never involved. Trade-off: loses ArchiveBox's automation and full-page-fidelity archiving, and manual copy-paste is more prone to skipping the Data Quality Principles (captions, consistent structure) than a script would be.

---

## 8. What Nobody Else Has Built

Ollama + Open-WebUI is the dominant self-hosted AI stack in 2026 — mainstream, not exotic. But the specific synthesis here — per-person GitJournal vaults kept deliberately outside the server, a git-based Shared Library as the sole ingestion path, household governance, tested backup restores, non-technical onboarding, all sized and priced for a household rather than an enterprise — isn't something anyone's packaged. Homelab guides are uniformly single-operator tutorials; none treat a household with mixed technical comfort as a first-class design concern.

**Security posture already matches 2026 best practice on the two things that matter most:** no open router ports, Tailscale-only access; and Ollama's own port is never reachable directly, only through Open-WebUI.

---

## 9. Trade-Offs, Stated Plainly

- **Capability gap:** less capable than frontier commercial models on complex, multi-step reasoning.
- **One-at-a-time queue:** by design, not a bug — see Executive Summary.
- **Two-app UX:** GitJournal for notes, Open-WebUI for chat — a real cost against any single commercial app, the price of the privacy architecture.
- **No vendor for the custom glue code** (Shared Library sync via `oikb`, the optional status widget) — see companion checklist for the operational-risk breakdown.
- **No hardware failover** — a single-PC design; accepted risk, partially mitigated by a UPS for graceful shutdown during power outages (1500VA/1000W pure sine wave, ~30 min runtime — decided, see `hardware-power.md`). Protects against disk corruption from a hard power-cut, doesn't solve the underlying single-point-of-failure.

---
*Draft v21 — supersedes v20. §7a: resolves the Headscale question from "recorded, not adopted" to a decision — hybrid by phase, Tailscale SaaS for Phase 1, self-hosted Headscale for Phase 2. [date: 2026-09-17]*
