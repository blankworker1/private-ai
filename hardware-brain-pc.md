# Hardware — Household Brain AI PC

Tracks the Phase 1 hardware decision end-to-end: from the spec's original hardware assumption through supplier evaluation, the specific model and configuration ordered, and the setup sequence once it arrives. Companion to `spec-home-ai.md` (§5 Shared AI Brain, §4a Shared Library Backup) and `hardware-tablet.md` (the equivalent record for the five Personal Layer devices) — this document is the live decision record; the spec's §5 cost table stays as the original planning estimate.

---

## 1. Original Spec Assumption

Spec §5 costed a DIY build around a discrete GPU, 16GB VRAM tier (e.g. RTX 4070 Ti Super), estimated **€1,400–1,800** for a full system (Sept 2026, inflated by an ongoing global DRAM shortage — flagged in the spec as elevated, not baseline).

## 2. Alternative Considered — Framework Desktop (AMD Ryzen AI Max)

Evaluated and set aside as the *first* purchase, though not ruled out for later:
- Different architecture entirely — unified LPDDR5x memory shared between CPU/GPU (BIOS-allocated "VRAM"), not a discrete GPU with dedicated VRAM.
- **Upside:** far more usable model-memory per euro — the 128GB config can allocate up to ~96GB as VRAM, enough for genuinely large models (Llama 3.3 70B-class) a 16GB discrete card can't hold at all.
- **Downside:** bandwidth-bound rather than compute-bound — likely slower token generation on small/mid models than a discrete GPU, despite the much higher size ceiling.
- **Real, documented risk specific to this chip:** community-reported GPU hangs / SMU deadlocks under heavy LLM inference on Linux, plus early-unit PSU/thermal issues — improving via BIOS updates but a genuine new risk class versus a standard discrete-GPU build.
- **Decision:** not pursued for Phase 1. A discrete-GPU, pre-tested vendor build was preferred for the first trial, keeping the Framework Desktop's real advantage (huge cheap unified memory) as a possible future option once the household design is validated.

## 3. Supplier — Radiance Systems

**Auriol, France (13390) — hand-assembled, load-tested before shipping, delivered EU-wide.**

Chosen over building a DIY kit because:
- Assembled and load-tested before shipping, not just dropshipped components.
- Ollama + Open-WebUI (or LM Studio) available pre-installed and configured on request — matches the spec's exact software stack rather than requiring adaptation.
- 2-year warranty, EU-based support.

Other EU options noted but not pursued for Phase 1: **AIME GmbH** (Berlin) — larger-scale deep learning workstations/servers, comprehensive burn-in testing, GDPR-compliant German colocation option — sizing and pricing lean enterprise/research scale rather than household scale; kept in mind as a possible reference point or Phase 2 option, not a Phase 1 candidate.

## 4. Model Selected — CoreAI 16

- **Spec:** RTX 5060 Ti, 16GB VRAM — matches the household spec's §5 VRAM target directly.
- **Price:** €1,703.
- Sits close to the spec's original €1,400–1,800 line, though above it — see §6 cost tally.

## 5. Turnkey AI Pack — €499 (incl. VAT)

Radiance's paid add-on (not included in the base CoreAI 16 price). **Confirmed by Radiance (15 Sept reply):** built on Ollama + Open-WebUI specifically — multi-user accounts and knowledge bases, not a single-user tool like LM Studio.
- Local AI environment — complete software stack installation.
- Pre-shipment testing — full functionality check before shipping.
- 3 pre-installed AI models (see §5a — upgraded from the originally-listed 2, at no extra cost).
- Secure remote access.

## 5a. Finalized Configuration (per Radiance's 15 Sept reply)

All items from §7's request confirmed included in the €499 Turnkey Pack at no extra cost:

- **OS:** Ubuntu 26.04 LTS.
- **Ollama:** `OLLAMA_NUM_PARALLEL=1`, `OLLAMA_MAX_LOADED_MODELS=2`, `OLLAMA_KEEP_ALIVE=-1` — exactly as requested.
- **Open-WebUI:** single admin account shipped; Carl creates the five household accounts personally. Per-account chat/document isolation tested by Radiance with two temporary accounts before shipping, then deleted.
- **Docker:** image pinned to a fixed version tag, documented in delivery notes — no auto-updates.
- **Tailscale:** installed, left unauthenticated for Carl's own tailnet.
- **`oikb`:** installed and tested against a throwaway repo.
- **Storage:** 1TB NVMe SSD (base) — see §6a for the sizing decision.

**Models (3, not 2 — added at no cost):**
- Gemma 4 12B — main model, loaded at startup.
- Qwen 3.5 9B — mid-size.
- Qwen 3.5 4B — fast, loaded at startup alongside the 12B.

**How the VRAM/concurrency settings actually behave in practice (Radiance's own engineering notes, useful to have on record):**
- With `MAX_LOADED_MODELS=2` and `KEEP_ALIVE=-1`, two models stay resident in the 16GB VRAM permanently. Switching to the third (non-resident) model triggers an automatic swap taking a few seconds — not a failure, just a brief, expected delay.
- The 12B + 9B pairing wouldn't fit together with a useful context size, which is why Radiance defaults to pairing the 12B with the 4B instead. Worth knowing if model choice ever needs revisiting.
- `NUM_PARALLEL=1` means simultaneous requests queue rather than run together — confirms the spec's own designed one-at-a-time model (§6) is exactly what's shipping. Radiance offered `NUM_PARALLEL=2` (two people generating at once) as an option, trading off higher VRAM use per loaded model; **declined — staying with the spec's `NUM_PARALLEL=1` as designed.**

## 6. Cost Tally (Phase 1, as quoted — D202609-509)

| Item | Cost |
|---|---|
| CoreAI 16 + Turnkey AI Pack, base config (as decided in §6a) | — |
| **Quoted total, incl. French/EU VAT** | **€2,362.31** |

Against the spec's €1,400–1,800 estimate: meaningfully over, but judged acceptable — this buys vendor assembly/burn-in testing and full household-specific configuration completed by Radiance at no extra charge (confirmed §5a), removing essentially all of the afternoon of setup work that was the alternative.

**Shipping destination: Italy.** Intra-EU (France → Italy), so the quoted total is genuinely final — no import VAT, customs declaration, or DDP/DDU question, unlike a UK destination would have raised.

**Terms:** 30% deposit on order, balance due before shipping. Preparation time: 15 business days from order. Quote valid until 15 October 2026.

## 6a. RAM and Storage — Decided

- **System RAM:** staying with the **base 16GB (1×16GB)**, declining Radiance's suggested 32GB (2×16GB dual-channel) upgrade. Their engineers flagged 16GB as potentially tight for Docker + Open-WebUI + indexing across five users, but the upgrade quoted at **€726** (elevated by the same DRAM shortage flagged in spec §5), and the household doesn't expect genuinely simultaneous heavy use across all five accounts — decided not worth the cost for Phase 1. Worth revisiting if real usage proves this wrong.
- **Storage:** staying with the **base 1TB NVMe**, no upgrade. Git-based markdown libraries are small (spec §1); 1TB comfortably covers Phase 1 with room to spare.

## 7. Custom Configuration Requested — Status: Resolved (15 Sept reply)

Original outreach asked whether the Turnkey Pack defaults to Ollama + Open-WebUI, and whether a list of household-specific settings fell within the existing €499 scope. **Radiance's reply confirmed all of it — see §5a for the finalized configuration.** Shipping (Italy) also confirmed — see §6.

Email also flagged, as a non-binding heads-up, that a separately-configured Community AI node build (Phase 2) may follow later — Radiance acknowledged and welcomed a future inquiry.

## 8. Connectivity Context

Home connection: **Starlink Standard** — typically 50–150 Mbps in practice, variable by time of day. Relevant to:
- Initial model download time if not fully pre-loaded (a ~9GB 14B model: ~12–15 min at ~80–100 Mbps; a 2–3 model starter set: ~40–50 min) — one practical point in favor of the Turnkey Pack's pre-loaded models.
- Day-to-day Tailscale remote access and Shared Library sync (§4/§7 of the spec) — both low-bandwidth, should be comfortable on this connection even if not on Starlink's best moments.

## 9. Setup Sequence — After Arrival

Even under the "plug and play" Turnkey Pack, these steps remain — each requires Carl's own accounts/credentials, which Radiance has no access to:

1. **Unbox and connect to Starlink** — Ethernet preferred over Wi-Fi for a server that needs to stay reliably reachable.
2. **Verify what Radiance actually delivered** — confirm Ollama/Open-WebUI running, all 3 pre-loaded models respond (Gemma 4 12B and Qwen 3.5 4B loaded at startup; Qwen 3.5 9B swaps in on demand — §5a), Docker image tag matches what was documented.
3. **Join the Tailscale tailnet** — authenticate the pre-installed client, set the ACL restricting the server to named household accounts (spec §7). The one networking step Radiance explicitly can't do.
4. **Set up Open-WebUI accounts** — create the 5 individual logins now if Radiance left a single admin account, before any shared chat history accumulates.
5. **Create the real Shared Library repo** on the machine and point `oikb` at it (Radiance's testing used a throwaway repo).
6. **Set up each person's Personal Layer** — GitJournal on Android, joined to the tailnet, initial push to both backup remotes (GitHub + local-only).
7. **Run the backup restore test** (implementation checklist, item 1) — a stated Phase 1 success criterion, not optional polish.
8. **Confirm the remaining Phase 1 success criteria** from the spec's Executive Summary — response times reasonable on everyday tasks, Shared Library sync run end-to-end at least once, Personal Layer functional with the Brain PC offline.

## 10. Possible Content Idea (not a build decision)

Considered: filming the unboxing (Radiance's build/Turnkey Pack contents) as Part 1, and the §9 setup sequence as Part 2 — a natural "does it actually become usable" follow-up. If pursued, worth contacting Radiance before filming, both re: their comfort with a customer demonstrating a paid config's contents, and because they may want to share or support well-made setup content themselves.

---

*v4 — supersedes v3. Updates step 6 of the setup sequence from Obsidian + GitSync to GitJournal, matching the Personal Layer editor decision recorded in `hardware-tablet.md` §5. [date: 2026-09-15]*
