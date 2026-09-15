# Hardware — Power Backup (UPS / Off-Grid)

Live decision record for backup power covering the Brain PC, Starlink Standard, and router — automatic failover on mains outage. Companion to `spec-home-ai.md` (§9 "no hardware failover" risk) and `hardware-brain-pc.md`. Two deliberately separate phases, not one purchase — see §3.

---

## 1. Load Profile

| Component | Idle/typical | Peak |
|---|---|---|
| Brain PC (CoreAI 16, RTX 5060 Ti) | ~60–80W | ~280–320W (active generation — GPU alone is 180W TDP) |
| Starlink Standard (dish + built-in router) | ~50–65W | ~75–130W (130W only under cold-weather snow-melt heater; unlikely in Italy) |
| Separate home router | ~10–15W | ~15W |
| **Total** | **~120–160W average** | **~390–450W peak** |

Two different numbers, two different jobs: **peak** sizes the UPS/inverter's continuous power rating (must never brown out at the worst simultaneous moment); **average** determines how long a given battery capacity actually lasts, since peaks are brief.

## 2. Requirement — Genuinely Automatic

"Automatic" specifically requires a fast transfer time (sub ~20–30ms) on power loss — not all battery/backup products qualify. A line-interactive UPS satisfies this inherently (typically a few ms). A "solar generator"-class power station needs its UPS/EPS pass-through mode checked explicitly; some switch too slowly and the Brain PC's PSU sees it as a power loss and reboots anyway, defeating the point.

## 3. Phase 1 — Small UPS, ~30 Minutes

**Decided:** 1500VA/1000W pure sine wave, line-interactive UPS, budget ≤€250 (e.g. CyberPower CP1500PFCLCD-class).

- **Pure sine wave is non-negotiable.** The Brain PC's PSU almost certainly has Active PFC (standard on modern GPU systems) — a cheaper simulated/stepped sine wave UPS can cause a shutdown or reboot at the exact moment it switches to battery.
  
- **1000W continuous rating** against a ~390–450W peak gives real headroom, rather than a 750VA/500W unit running near its limit during GPU-active bursts.
  
- **Runtime:** rated for ~25 min at 300W in this UPS class; at this setup's lower ~140–160W average, realistically 45–60 min — comfortable margin over the 30-minute target.
  
- **Automatic transfer:** inherent to any genuine line-interactive UPS (§2) — no extra selection criteria needed beyond confirming the unit is a real UPS, not a manual-switch battery box.
  
- **Before buying:** confirm pure sine wave on the actual listing — not all budget UPS units at similar VA ratings are, despite looking equivalent on spec sheets.
  
- **Scope:** covers Brain PC + Starlink Standard + router on its battery-backed outlets. Supersedes the earlier, less-specified ~$190/10–12-minute placeholder from the original checklist item.

## 4. Phase 2 — Off-Grid Solar + Battery

**Not started.** A different category of purchase entirely, not an extension of Phase 1's budget.

- **Target:** ride through a full 24-hour outage, not just bridge/shutdown.
- **Sizing:** 160W average × 24h = 3,840Wh, plus ~15% for inverter losses and battery reserve → **~4,500Wh usable capacity needed**.
- **Category:** expandable power-station platforms (EcoFlow Delta Pro / Bluetti AC300+B300-class, stacked battery packs to reach ~4.5–5kWh) rather than a standard UPS.
- **Estimated cost:** ~€2,500–4,500 for hardware at this scale.
- **Why solar changes the sizing:** pure battery for 24 hours with zero recharge is the expensive way to solve this. Battery + solar panel input means the battery tops back up through daylight hours rather than draining continuously — a genuinely smaller, cheaper battery can then cover the same real-world outage. Deployment to Italy makes daylight availability a real asset here, not theoretical.
- **Same automatic-transfer requirement as §2 applies** — confirm genuine UPS/EPS pass-through mode on whatever platform is eventually chosen, not assumed from "has a battery."

---

*v1 — first version of this document. [date: 2026-09-15]*
