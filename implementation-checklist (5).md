# Implementation Checklist

Companion to the main spec (Draft v14). The spec no longer contains its own checklist section (removed in v9) — this document is the sole source of truth for open items. 17 open items, in one place. Three have full step-by-step breakdowns (they're genuine multi-step build/test procedures); the rest are decisions — a choice to record, not a procedure to run. Items 13–16 were added following an external review; item 5 uses Open-WebUI's official `oikb` tool rather than a custom sync script; item 17 supports the spec's §4a backup design.

---

## BEFORE FUNDING

### 1. Backup Restore Test — detailed
**Why it's here:** proves the backup design works, before spending on hardware. Costs nothing, needs no GPU, runs on whatever you have today.

**Status:** Not yet run.

1. **Build a disposable test vault**
   - [ ] Create a new, throwaway Obsidian vault (not the real one)
   - [ ] Add 2–3 markdown notes with YAML frontmatter
   - [ ] Clip one image via Save as Local Images, confirm it's a local file, not a hotlink

2. **Push to both remotes**
   - [ ] Push the test vault to a private GitHub repo
   - [ ] Push the same test vault to a bare repo on the local-only remote (USB/NAS)
   - [ ] Add one commit to each containing a unique, identifiable nonsense word (for the history check in step 4)

3. **Simulate total loss**
   - [ ] Clone from GitHub into a completely fresh directory (not a reclone in place)
   - [ ] Separately, clone from the local-only remote into another fresh directory

4. **Verify integrity**
   - [ ] Checksum/diff restored text files against the originals — confirm byte-for-byte match
   - [ ] Checksum/diff the restored image file specifically
   - [ ] Run `git log --all -S"your-test-word"` on each restored clone — confirm it's found (proves history transferred, not just the latest snapshot)

5. **Repeat via GitSync on Android**
   - [ ] Clone the same test repo using GitSync on a phone/tablet
   - [ ] Repeat the integrity checks from step 4 on this restored copy
   - [ ] Note any difference in behavior vs. the desktop git restore (GitSync uses a different underlying implementation — this is the actual path most household members would use)

6. **Record the result**
   - [ ] Date tested:
   - [ ] GitHub remote restore: pass / fail (notes: )
   - [ ] Local-only remote restore: pass / fail (notes: )
   - [ ] GitSync (Android) restore: pass / fail (notes: )
   - [ ] Update the main spec's Executive Summary success criteria with this result

### 2. Off-Site Backup Location
- [ ] Decide where the local-only remote's second physical copy lives (protects against fire/theft/flood at the primary location)

### 3. GitHub Repo Privacy
- [ ] Confirm public vs. private for the GitHub remote

### 4. Build Order
- [ ] Decide the actual sequence: server/GPU first, Tailscale first, or Personal Layer first

---

## BUILD ITEMS

### 5. Shared Library Sync — detailed
- [ ] Confirm `oikb` setup against the Shared Library repo (replaces the earlier hand-built cron/Task Scheduler script — removes the single largest silent-failure risk in the previous design)
- [ ] Decide the sync interval: hourly, nightly, or manual-trigger-only
- [ ] Test end-to-end at least once: new file pushed to Shared Library → appears in Open-WebUI's Knowledge Base without manual intervention beyond initial setup

### 6. Status Widget — detailed
- [ ] Build the small metrics-proxy backend endpoint exposing "is someone generating right now"
- [ ] Wire Android HTTP Shortcuts to poll it (target: 15–30s interval)
- [ ] **Real-device test:** confirm the widget updates correctly while someone else is actively generating
- [ ] **Record result:** date tested, confirmed working
- [ ] Non-critical — safe to defer past Phase 2 rollout if time is short (see spec Executive Summary)

### 7. Shared Library Retention Policy
- [ ] Decide a pruning/retention approach as the Shared Library's disk usage grows over time

### 8. Software Update Ownership
- [ ] Decide who updates Ollama / Open-WebUI / models over time, and how often
- [ ] Pin Open-WebUI's Docker image tag (update deliberately, not automatically — one guide flags occasional breaking changes between versions as the stack's one real rough edge)

### 9. Build Timeline Estimate
- [ ] Estimate a start-to-finish timeline for the whole build

### 10. Real-Device Test — Simultaneous Demand
- [ ] Confirm the one-at-a-time GPU queue behaves as expected under real simultaneous household demand (Phase 2 success criterion)

### 11. UPS / Power Protection — Decided, Two Phases
- [x] **Phase 1 (now):** 1500VA/1000W pure sine wave line-interactive UPS, ≤€250 (e.g. CyberPower CP1500PFCLCD-class), covering Brain PC + Starlink Standard + router — targets ~30 min runtime, comfortable headroom over the ~390–450W peak load. Pure sine wave required (Active PFC PSU compatibility); automatic transfer is inherent to any genuine line-interactive UPS.
- [ ] **Phase 2 (deferred):** off-grid solar PV + larger battery, sized for a full 24-hour outage (~4.5kWh usable capacity) — a separate, much larger purchase (~€2,500–4,500), not part of this item's budget. See `hardware-power.md`.
- [ ] Purchase Phase 1 unit, confirm pure sine wave spec on the actual listing before buying (not all budget UPS units are, despite similar VA ratings)


---

## AFTER FUNDING / ONGOING

### 12. Shared Library Governance
- [ ] Decide who has final say on what's appropriate to add to the Shared Library (it feeds everyone's AI answers, not just the contributor's)
- [ ] Decide how disputes get resolved if two people disagree about something in it
- [ ] Decide whether anything should be removable after the AI's already ingested it, and how (re-sync after deletion should handle this, but confirm it does)

### 13. Non-Technical User Onboarding
*Distinct from account setup — this is teaching actual day-to-day use.*
- [ ] Decide how less-technical household members learn the two-app model (Obsidian for notes, Open-WebUI for AI chat) without needing to understand the architecture behind it
- [ ] Consider a one-page "how to use this" cheat sheet, separate from this technical checklist
- [ ] Identify who in the household is comfortable troubleshooting for the others day-to-day

### 14. Exit Strategy
- [ ] Confirm and document the actual off-ramp if the trial doesn't work out: personal vaults are plain markdown + git (portable, no lock-in); Shared Library is the same; only the Ollama/Open-WebUI layer would be abandoned
- [ ] Decide what "doesn't work out" would concretely look like (ties back to the Phase 1 success criteria in the spec's Executive Summary)
- [ ] Estimate resale/repurposing value of the GPU hardware if abandoned (it's a general-purpose gaming/compute GPU, not single-purpose)

### 15. Disaster Recovery Beyond Backup
- [ ] Document what happens if the AI PC itself fails outright (no hardware failover by design — see spec §9) — who sources replacement hardware, how long the household goes without the platform

### 16. Local AI Fallback Decision
- [ ] Decide, one way or the other, whether a small offline fallback model is worth adding for Personal Layer devices when the Brain is unreachable — reconsider for Android specifically, since on-device inference options are more limited on mobile than desktop

### 17. Shared Library Backup — Drive & Reminder Script (spec §4a)
- [ ] Buy two external drives, sized against expected Shared Library growth
- [ ] Decide the backup interval (e.g. weekly)
- [ ] Decide the off-site rotation interval — need not match the backup interval (e.g. monthly swap)
- [ ] Decide the off-site location(s) for the drive not currently in use
- [ ] Build the scheduled script: reads last-backup timestamp, compares against the decided interval, sends a Tailscale-reachable notification once overdue
- [ ] Decide how the notification surfaces — a message inside Open-WebUI, or a plain system/push notification — a presentation choice on top of the same underlying trigger
- [ ] Test end-to-end at least once: connect drive, run backup, disconnect, confirm the script correctly reads the new timestamp and doesn't fire a false "overdue" notice

---

*Companion to spec Draft v18. Update the spec's Executive Summary once items are completed here.*
