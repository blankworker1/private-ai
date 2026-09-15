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

## 5. Turnkey AI Pack — €499

Radiance's paid add-on (not included in the base CoreAI 16 price):
- Local AI environment — complete software stack installation.
- "Simplified interface" — description doesn't explicitly name Open-WebUI; confirmed as an open question to Radiance directly (see §7), since the spec's multi-user/knowledge-base needs specifically require Open-WebUI, not a single-user tool like LM Studio.
- Pre-shipment testing — full functionality check before shipping.
- 2 pre-installed AI models.
- Secure remote access.

## 6. Cost Tally (Phase 1, as currently configured)

| Item | Cost |
|---|---|
| CoreAI 16 | €1,703 |
| Turnkey AI Pack | €499 |
| **Total** | **€2,202** |

Against the spec's €1,400–1,800 estimate: €400–800 over. Judged acceptable given this buys vendor assembly/burn-in testing and (pending §7's answer) vendor-completed household-specific configuration — the trade being weighed is "pay more for fully plug-and-play" vs. "buy the bare CoreAI 16 and do the software setup personally," the latter being realistically an afternoon's work, not a large undertaking.

## 7. Custom Configuration Requested — Status: Email Sent, Awaiting Reply

Outreach email sent to Radiance asking:
1. Whether the Turnkey Pack installs Ollama + Open-WebUI by default, or a different interface.
2. Whether the following fall within the existing €499 scope, or would be quoted as additional customization:
   - Ollama env vars: `OLLAMA_NUM_PARALLEL=1`, `OLLAMA_MAX_LOADED_MODELS=2`, `OLLAMA_KEEP_ALIVE=-1`
   - Open-WebUI multi-user accounts (5 named, or single admin to configure personally)
   - Open-WebUI Docker image pinned to a specific version tag (not `:latest`)
   - Tailscale installed but left unauthenticated (joining the tailnet is a step only Carl can do — see §9)
   - `oikb` installed and confirmed working against a throwaway test repo
   - Linux (Ubuntu) rather than Windows, plus SSD size confirmation

Email also flagged, as a non-binding heads-up, that a separately-configured Community AI node build (Phase 2) may follow later — not part of this order.

## 8. Connectivity Context

Home connection: **Starlink Standard** — typically 50–150 Mbps in practice, variable by time of day. Relevant to:
- Initial model download time if not fully pre-loaded (a ~9GB 14B model: ~12–15 min at ~80–100 Mbps; a 2–3 model starter set: ~40–50 min) — one practical point in favor of the Turnkey Pack's pre-loaded models.
- Day-to-day Tailscale remote access and Shared Library sync (§4/§7 of the spec) — both low-bandwidth, should be comfortable on this connection even if not on Starlink's best moments.

## 9. Setup Sequence — After Arrival

Even under the "plug and play" Turnkey Pack, these steps remain — each requires Carl's own accounts/credentials, which Radiance has no access to:

1. **Unbox and connect to Starlink** — Ethernet preferred over Wi-Fi for a server that needs to stay reliably reachable.
2. **Verify what Radiance actually delivered** — confirm Ollama/Open-WebUI running, the 2 pre-loaded models respond, Docker image tag matches what was documented.
3. **Join the Tailscale tailnet** — authenticate the pre-installed client, set the ACL restricting the server to named household accounts (spec §7). The one networking step Radiance explicitly can't do.
4. **Set up Open-WebUI accounts** — create the 5 individual logins now if Radiance left a single admin account, before any shared chat history accumulates.
5. **Create the real Shared Library repo** on the machine and point `oikb` at it (Radiance's testing used a throwaway repo).
6. **Set up each person's Personal Layer** — Obsidian + GitSync on Android, joined to the tailnet, initial push to both backup remotes (GitHub + local-only).
7. **Run the backup restore test** (implementation checklist, item 1) — a stated Phase 1 success criterion, not optional polish.
8. **Confirm the remaining Phase 1 success criteria** from the spec's Executive Summary — response times reasonable on everyday tasks, Shared Library sync run end-to-end at least once, Personal Layer functional with the Brain PC offline.

## 10. Possible Content Idea (not a build decision)

Considered: filming the unboxing (Radiance's build/Turnkey Pack contents) as Part 1, and the §9 setup sequence as Part 2 — a natural "does it actually become usable" follow-up. If pursued, worth contacting Radiance before filming, both re: their comfort with a customer demonstrating a paid config's contents, and because they may want to share or support well-made setup content themselves.

---

*v1 — first version of this document, consolidating decisions made across the hardware-selection discussion. [date: 2026-09-15]*
