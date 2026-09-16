# Hardware — Personal Layer (Android Devices)

Tracks the Personal Layer hardware decision end-to-end, per household member: from the spec's software requirements through device selection criteria and the per-device setup sequence. Companion to `spec-home-ai.md` (§2 Personal Layer — Android) and `hardware-brain-pc.md` — this document is the live decision record for the five individual devices, the way the other file is for the one shared server.

---

## 1. Original Spec Assumption

Spec §2–3: no GPU required on either the Android or desktop path — the Personal Layer is a markdown editor and a git client, nothing more. Confirmed against actual minimum requirements rather than just assumed:

| App | Minimum | Notes |
|---|---|---|
| GitJournal | Android/iOS, actively maintained (Flutter-based) | Combines what were previously two separate requirements (editor + git client) into one |
| Tailscale | Comparably low, actively maintained | |

**Conclusion:** essentially any Android phone or tablet sold in the last several years meets every software requirement. This is a deliberate property of the design (spec §2), not a coincidence.

## 2. Why This Isn't a "Supplier" Search

Unlike the Brain PC (`hardware-brain-pc.md`), there's no AI-optimized vendor question here — no GPU, no local inference, nothing for a specialist EU AI-hardware supplier to add value on. This is an ordinary consumer tablet/phone decision, governed by daily-use criteria rather than compatibility (already settled, §1).

## 3. Selection Criteria (Given Compatibility Is a Non-Issue)

- **Screen size** — a tablet beats a phone for real writing sessions; a phone suits quick capture/reference only.
- **RAM** — GitJournal is lighter than the previous Obsidian + plugin stack; no specific minimum confirmed, but low-end devices are less of a concern than under the old three-app setup.
- **Keyboard support** — relevant if anyone plans to type at length; determines whether a Bluetooth/folio-keyboard-compatible model matters for that person.
- **Storage** — not a real constraint; vaults are plain markdown, tiny by modern standards even on base-storage devices.

## 4. Device Decision — Five Existing/Random Devices, No New Purchases

**Decided:** for Phase 1, the trial runs on five existing or otherwise already-available Android phones/tablets — deliberately not a matched set of new devices. Reasoning: the spec's own compatibility claim (§1) is that essentially any Android device meets the software floor; validating that against genuine device variety (different ages, manufacturers, OS versions) is a stronger real-world test than five identical new units would be, and costs nothing.

**Worth checking per device before onboarding, given they're not freshly provisioned:**
- **Play Store availability** — not every "Android" device has it by default (e.g. Amazon Fire tablets run a Play-Store-less fork); confirm each candidate actually has Play Store access before assuming it fits.
- **Android version** — quick check against current GitJournal requirements rather than assuming, particularly on older or budget devices whose OEM may have stopped OS updates early.
- **Available storage** — vaults themselves are tiny, but a device with prior heavy use may already be close to full from its previous life.

## 5. Personal Layer Editor — GitJournal (Decided)

**Replaces the original three-app stack (Obsidian + GitSync + Save as Local Images) with one app.** Full evaluation happened conversationally rather than in a document — recorded here as the decision record:

- **Open source** — AGPL-3.0/Apache-2.0 mix, consistent with the project's own license choice (`LICENSE.md`).
- **Git sync is native, not bolted on** — auto-commits on every change, auto-pushes by default (configurable). No separate GitSync app needed.
- **Images insert directly into the repo** — no separate plugin needed, unlike Obsidian's Save as Local Images.
- **Genuine backlinks** — wiki-style `[[links]]`, with each note showing what links to it, not just outgoing links.
- **What it doesn't have:** no web clipping — resolved architecturally, not by the app (see spec-home-ai.md §2/§5 — clipping is now a decoupled Brain PC service, not an editor feature at all).
- **Maintenance profile, stated honestly:** small-team open-source project (primarily one maintainer, Vishesh Handa). Confirmed real commit activity as recently as May 2026 — not abandoned — but open issues sometimes sit unresolved for a while, consistent with limited maintainer bandwidth. Acceptable risk given the vault stays plain markdown + git regardless of app; switching editors later costs re-onboarding, not data loss.
- **Monetization:** pay-what-you-want for "Pro" features — but the Pro feature code itself is open source too, not gated. Paying supports development and gets the convenient packaged version; nothing architectural is closed off either way.

## 6. Connectivity Context

Home connection: Starlink Standard (see `hardware-brain-pc.md` §8 for detail). Largely irrelevant here — the entire Personal Layer app set (§1) is small, trivial at any Starlink speed tier. The connectivity consideration that mattered (large model downloads) is a Brain PC concern only.

## 7. Setup Sequence — Per Device

Same steps, repeated once per household member's device. Each step requires that person's own accounts/credentials (Tailscale, GitHub), same reasoning as the Brain PC's setup sequence being non-fully-outsourceable:

1. **Install GitJournal and Tailscale** — both free via the Play Store.
2. **Join the household Tailscale tailnet** — this person's own named account under the ACL, not a shared login.
3. **Connect GitJournal to the personal vault** — point at this person's own GitHub remote + local-only remote via SSH; initial push (new vault) or first clone (migrating from an existing setup).
4. **Reach Open-WebUI over Tailscale** — browser to the Brain PC's Tailscale address, log into this person's individual account, send a test prompt.
5. **Clone the Shared Library** — separate from the personal vault; confirm this person can push into their own subfolder (spec §4).
6. **Test offline (airplane mode)** — confirm GitJournal opens, edits, and saves with networking off entirely. One of the spec's own stated Phase 1 success criteria (personal notes independent of the Brain PC).

## 8. Backup, As a Side Effect of Normal Use

Worth restating here since it's a Personal Layer property specifically: step 3 above means ordinary multi-device convenience (wanting the same vault on phone and tablet) produces a tested, dual-remote backup (GitHub + local-only, spec §2) as a side effect — no separate "remember to back up" discipline required, and GitJournal's auto-commit/auto-push behavior makes this even more automatic than the previous GitSync setup's manual sync button. This is the personal-vault half of the two-incentive design discussed alongside the spec; the Shared Library's stronger, AI-access-gated version of the same idea is covered in spec §4/§4a.

## 9. Possible Content Idea (not a build decision)

Same video series under consideration for the Brain PC (`hardware-brain-pc.md` §10): this device's setup sequence as a further part, likely filmed once and described as identical per person rather than repeated five times.

---

*v3 — supersedes v2. Personal Layer editor decided: GitJournal replaces Obsidian + GitSync + Save as Local Images (§5, new section). Setup sequence shortened from 7 to 6 steps accordingly. Maintenance and licensing findings recorded from the evaluation discussion. [date: 2026-09-15]*
