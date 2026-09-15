# Hardware — Personal Layer (Android Devices)

Tracks the Personal Layer hardware decision end-to-end, per household member: from the spec's software requirements through device selection criteria and the per-device setup sequence. Companion to `local-ai-spec-draft-v14.md` (§2 Personal Layer — Android) and `hardware-brain-pc.md` — this document is the live decision record for the five individual devices, the way the other file is for the one shared server.

---

## 1. Original Spec Assumption

Spec §2–3: no GPU required on either the Android or desktop path — the Personal Layer is a markdown editor and a git client, nothing more. Confirmed against actual minimum requirements rather than just assumed:

| App | Minimum | Notes |
|---|---|---|
| Obsidian | Android 7.0+ | Sets the effective floor for the stack |
| GitSync | Android 5+ | Lower than Obsidian's own floor |
| Mobile Webviewer | Rides on Obsidian's minimum | No additional requirement |
| Tailscale | Comparably low, actively maintained | |

**Conclusion:** essentially any Android phone or tablet sold since ~2017 meets every software requirement. This is a deliberate property of the design (spec §2), not a coincidence.

## 2. Why This Isn't a "Supplier" Search

Unlike the Brain PC (`hardware-brain-pc.md`), there's no AI-optimized vendor question here — no GPU, no local inference, nothing for a specialist EU AI-hardware supplier to add value on. This is an ordinary consumer tablet/phone decision, governed by daily-use criteria rather than compatibility (already settled, §1).

## 3. Selection Criteria (Given Compatibility Is a Non-Issue)

- **Screen size** — a tablet beats a phone for real writing sessions; a phone suits quick capture/reference only.
- **RAM** — 4GB commonly cited as a comfortable minimum for Obsidian with a few plugins active; below that, vault search/rendering can lag on larger vaults.
- **Keyboard support** — relevant if anyone plans to type at length; determines whether a Bluetooth/folio-keyboard-compatible model matters for that person.
- **Storage** — not a real constraint; vaults are plain markdown, tiny by modern standards even on base-storage devices.

## 4. Device Decision — Status: Open

**Open question, not yet resolved:** are these new purchases for household members, or existing phones/tablets already in the house being confirmed suitable? This changes the shape of the decision — "here are budget tablet picks" vs. "confirm what's already here meets §1" — and hasn't been settled yet.

## 5. Connectivity Context

Home connection: Starlink Standard (see `hardware-brain-pc.md` §8 for detail). Largely irrelevant here — the entire Personal Layer app set (§1) totals well under 1GB combined, trivial at any Starlink speed tier. The connectivity consideration that mattered (large model downloads) is a Brain PC concern only.

## 6. Setup Sequence — Per Device

Same steps, repeated once per household member's device. Each step requires that person's own accounts/credentials (Tailscale, GitHub), same reasoning as the Brain PC's setup sequence being non-fully-outsourceable:

1. **Install the core apps** — Obsidian, GitSync, Tailscale, all free via the Play Store.
2. **Add Obsidian plugins** — Mobile Webviewer and Save as Local Images, installed from inside Obsidian's own Community Plugins browser, not the Play Store.
3. **Join the household Tailscale tailnet** — this person's own named account under the ACL, not a shared login.
4. **Connect GitSync to the personal vault** — point at this person's own GitHub remote + local-only remote; initial push (new vault) or first clone (migrating from desktop).
5. **Reach Open-WebUI over Tailscale** — browser to the Brain PC's Tailscale address, log into this person's individual account, send a test prompt.
6. **Clone the Shared Library** — separate from the personal vault; confirm this person can push into their own subfolder (spec §4).
7. **Test offline (airplane mode)** — confirm Obsidian opens, edits, and saves with networking off entirely. One of the spec's own stated Phase 1 success criteria (personal notes independent of the Brain PC).

## 7. Backup, As a Side Effect of Normal Use

Worth restating here since it's a Personal Layer property specifically: step 4 above means ordinary multi-device convenience (wanting the same vault on phone and tablet) produces a tested, dual-remote backup (GitHub + local-only, spec §2) as a side effect — no separate "remember to back up" discipline required. This is the personal-vault half of the two-incentive design discussed alongside the spec; the Shared Library's stronger, AI-access-gated version of the same idea is covered in spec §4/§4a.

## 8. Possible Content Idea (not a build decision)

Same video series under consideration for the Brain PC (`hardware-brain-pc.md` §10): this device's 7-step sequence as a further part, likely filmed once and described as identical per person rather than repeated five times.

---

*v1 — first version of this document. [date: 2026-09-15]*
