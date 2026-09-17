# Micro Brain PC & Power Control Node

**Draft v1** — 2026-09-17

Two distinct hardware applications, both surfaced from the same starting point (evaluating ClawBox), and both worth keeping as separate concepts even though they share an origin. Concept stage — not yet built.

---

## Why these two live in one document, but stay conceptually separate

Both ideas came from asking the same question — "could Jetson/edge-AI-class hardware fit into private-ai's ecosystem?" — but they answer two different needs, use two different hardware tiers, and (critically) sit on opposite sides of the project's own "no live AI agency in a write path" line. Worth reading them as siblings, not variants of one thing.

|| Micro Brain PC | Power Control Node |
|---|---|---|
| **What it is** | A miniature, single-person Home AI stack | A deterministic sense/actuate/report layer for solar + mining hardware |
| **AI involved?** | Yes — genuine Ollama + Open-WebUI chat, same stack as the household Brain | No — fixed-rule automation only, no AI judgment anywhere in the control loop |
| **Hardware class** | Jetson Orin Nano Super (67 TOPS, AI-capable) | Raspberry Pi / Home Assistant Yellow-Green class (no AI compute needed) |
| **Approx. cost** | €654.50, confirmed (reichelt, incl. VAT) | €60–150 |
| **Physical stakes if it misbehaves** | Low — worst case, a bad chat answer | Real — miner damage, battery health, inverter settings |

---

## 0. The low-cost base layer — shared components, assembled differently

Together, these two applications produce a genuinely lower-cost entry point into the whole private-ai stack — a Micro Brain (€654.50, confirmed) plus an existing/already-owned tablet is roughly a quarter of the full Radiance Brain PC's cost (€2,362), not an order of magnitude cheaper as earlier, unconfirmed price estimates suggested, but still a real, meaningfully lower-cost tier — not a marketing simplification of the same offering.

**Worth being precise about what's actually shared, rather than treating this as one fixed three-item kit reused identically everywhere:**

- **Micro Brain + tablet is the genuinely shared pairing** — same hardware class, same software, same role, across both use cases below.
- **The quantity differs by use case.** The AI cafe exhibition's Fork B design needs *three* independent Micro Brain + tablet pairs — one fully separate station per concurrent seat, no shared queue. The personal/household base layer needs just *one*.
- **The Power Control Node is not a universal third item — it's an optional module specific to the household use case.** A pop-up exhibition stand has no Victron inverter or mining hardware to sense or actuate; there's nothing for a Power Control Node to do there. It only belongs in an assembly that actually has solar/mining hardware to manage.

**The two assemblies, concretely:**

| Use case | Micro Brain + tablet | Power Control Node |
|---|---|---|
| AI cafe exhibition | ×3 (one per seat, independent) | Not present |
| Personal / household base layer | ×1 | ×1 (optional, only if solar/mining hardware exists) |

**Power Control Node → Micro Brain connection, for the household assembly:** the Power Control Node's reporting job was always "push a Shape B snapshot to *a* Brain's Library" — nothing in that design assumes the target has to be the full household Brain specifically. Any Ollama + Open-WebUI + `oikb` instance is a valid ingestion target, so pointing it at a Micro Brain instead is a legitimate, no-new-mechanism substitution. This doesn't strain the Micro Brain's limited compute either — ingestion is the same lightweight, deterministic markdown-write regardless of target; the model's capability ceiling only matters at query time, not at ingestion time.

---

## 1. Micro Brain PC

### Concept

A complete, miniature Home AI stack — the same Ollama + Open-WebUI software the household Brain runs, on smaller, cheaper, single-person hardware, paired with a tablet as the interface. Not a compromise version of the household Brain; a genuinely different scale of the same architecture, for a different unit of ownership (one person, not a household).

### Capability, stated honestly

Jetson Orin Nano Super's 8GB unified memory realistically caps usable models at roughly the 4B-parameter class, quantized (e.g. Qwen3 4B). That is a genuinely lighter capability tier than the household Brain's RTX-class hardware — good for straightforward Q&A and Library lookups, not equivalent conversational depth. This should be stated plainly wherever this product is described, not glossed over.

### Why it's worth building anyway — three concrete uses already identified

1. **The AI cafe exhibition's Fork B, made real.** The exhibition design earlier forked between one shared Brain with capped concurrency (Fork A) and N independent, genuinely simultaneous stations (Fork B) — at the time, Fork B was flagged as newly viable but without specific hardware named. A Micro Brain PC per seat, paired with a tablet, *is* Fork B: three exhibition seats become three truly independent stations, no shared GPU queue, closer to the original webcafe's "N independent terminals" shape.
2. **A personal/travel kit.** Someone who wants their own complete stack independent of the household Brain — while travelling, or as a starter unit before committing to a full household purchase.
3. **A low-commitment CER starter Node.** An early-adopter producer-member trialling the Community AI concept before a full 5-person Node cluster forms.

More generally, it's a genuine second, lower-cost product tier — worth naming in any household-adoption growth thinking as the entry point before someone commits to a full Brain PC purchase.

### Software

Identical stack to the household Brain: Ollama (native Jetson CUDA support, single-command install, well documented), Open-WebUI on top, `oikb` for Library ingestion. Because it's the same stack, everything already built — Library format, GitJournal integration, `oikb`, Channels — works without any new integration work. This is the real strength of the idea, more than the hardware itself.

### Hardware — confirmed

ClawBox was the device that started this thread, but it turned out to be one vendor's packaged product on a widely-available hardware platform, not the only option — and not the right one for this specific use (its price premium pays mostly for OpenClaw, its AI-agent software layer, which this application deliberately doesn't use — see Power Control Node, below, for why agent-driven action is avoided here).

**Confirmed: Seeed reComputer J3011 (Orin Nano 8GB), via reichelt elektronik (Sande, Germany).**

- **Price: €654.50, incl. 19% VAT** — roughly €550 ex-VAT for a VAT-registered buyer able to reclaim it.
- **Stock: confirmed in stock, 1–2 business day delivery** at time of checking — resolves an earlier concern that several Jetson-class SKUs were showing as backordered elsewhere.
- **Scope of delivery, confirmed:** Jetson Orin Nano 8GB module + reComputer J401 carrier board, 128GB NVMe SSD (pre-installed), active heatsink, aluminium case, JetPack pre-installed, WiFi/BT pre-installed via M.2, 12V/5A power supply.
- **One easy-to-miss gap:** the power brick is included, but the country-specific mains plug/cord to it is not — budget for that separately.
- **Super Mode confirmed achievable on this exact unit, at no extra cost.** This is the base-clock "Classic" configuration (40 TOPS, JetPack 5.1.x pre-installed) rather than the pricier pre-flashed "Super" SKU some retailers sell (~$769) — but it's the *same physical board*. A free, official self-upgrade to JetPack 6.2 unlocks MAXN Super Mode, taking the same hardware to 67 TOPS — Seeed's own documentation states this explicitly. No reason to pay the pre-flashed premium for identical hardware. (One caution found while checking this applies specifically to the Orin *NX* module, not the Orin *Nano* used here — doesn't affect this unit, but worth knowing the warning exists so it isn't misapplied later.) The included active (fan-cooled, not passive) heatsink is adequate for Super Mode's higher power draw.

**Not pursued further:** the bare NVIDIA official devkit (~€519 in the EU, excluding storage and a case, which would need to be sourced separately) — the reComputer's all-in-one bundle at a comparable price makes the extra assembly work unnecessary.

---

## 2. Power Control Node

### Concept

A dedicated, deterministic control layer — separate and outside the household Brain — handling three jobs for the household's solar and mining hardware:

- **Sense** — Victron GX data via local MQTT (Home Assistant's native integration, no cloud).
- **Actuate** — apply a fixed power-limit rule to mining hardware (via `hass-miner`, supporting Antminer + BitAxe) and to inverter/charger settings, based on sensed PV and battery state.
- **Report** — push regular operational data to the Brain PC's Library via the existing Shape B pattern (deterministic snapshot, `oikb` ingests, AI reads only, never touches live).

This directly continues the existing curtailment-control design work (Home Assistant + `hass-miner`), previously planned for an Umbrel node — this document reframes it as its own named node type rather than a feature of another system.

### The one rule that must not be crossed

**No AI judgment anywhere in the actuate path.** This is deterministic, rule-based automation — sensed state in, a fixed threshold applied, a power limit out — the same category as a thermostat, not an agent deciding what to do. This is precisely why the existing curtailment design never conflicted with the household architecture's standing decision against live AI tool-access: there was never any AI agency in the loop to begin with. An AI agent (ClawBox's own OpenClaw, or any equivalent) must **not** be given this job — the physical stakes (miner damage, battery health) are real, unlike a chat answer, and reopening AI-driven action here would directly contradict `DECISIONS.md`'s existing "not trustless execution, a human still acts" principle.

### Hardware

Raspberry Pi or Home Assistant Yellow/Green class (~€60–150). Deliberately not Jetson-class — this job needs no AI compute at all, and paying for unused AI capability here would be the same mismatch flagged for using ClawBox as-is on this job.

### Relationship to the Micro Brain PC

Two separate small devices, two separate jobs, correctly sized for each: the Power Control Node never touches AI reasoning; the Micro Brain PC never touches physical actuation. Worth keeping this boundary explicit in any future build — the two ideas emerged from the same source (evaluating ClawBox) but should not be merged into one device.

---

## Status

Micro Brain PC: hardware confirmed — Seeed reComputer J3011, €654.50 via reichelt elektronik, in stock. Software path already proven (Ollama + Open-WebUI on Jetson, documented and working). Power Control Node: continues already-designed curtailment control work (Home Assistant + `hass-miner`), reframed here as a named node type; hardware and control logic already largely specified, not newly invented.

---

*v2 — supersedes v1. Micro Brain PC hardware moved from open selection to confirmed: Seeed reComputer J3011 via reichelt elektronik, €654.50, in stock, free Super Mode upgrade path confirmed. [date: 2026-09-17]*
