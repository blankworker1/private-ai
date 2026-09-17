# AI Cafe — Pop-up Exhibition Spec

**Draft v1** — 2026-09-17

A new, separate use case for private-ai — not part of Phase 1 (Home AI) or Phase 2 (Community/Coop AI), but built directly on Phase 1's hardware and stack. Concept stage: captures the design discussion so far, not yet built.

---

## 1. Concept

A live, facilitator-narrated public demo of the same self-sovereign AI experience the household uses, with a self-sovereign money layer (Lightning) bolted on top as the session trigger. The thesis the exhibit is meant to demonstrate through its own mechanics, not just explain: paying with Lightning means no card processor, no bank, no institution touching the payment; talking to the AI means no cloud, no training, nothing leaving the hardware. Both halves of the visitor's experience — the money and the conversation — stay outside institutional custody, back to back, on the same object.

Distinct from an unattended kiosk model (the earlier "AI cafe" framing considered and set aside): this is a staffed demo, one facilitator present throughout, walking visitors through a real sequence rather than an automated pay-and-walk-away flow. That removes most of the edge cases an unattended version would need (automated session wiping, hard cutoffs, anonymous account provisioning) — a human handles them live.

## 2. Hardware configuration

| Component | Role |
|---|---|
| Brain PC | Same spec as the Home AI Brain PC (Radiance CoreAI 16 tier) — demonstrates the identical experience, not a scaled-down one |
| Fixed Starlink dish | Home setup, part of the household's normal internet — unrelated to the exhibition's own connectivity |
| Mobile Starlink dish (Mini) | Self-contained connectivity for the exhibition's three tablets when trialed away from home — built-in WiFi router, ~15-20ft reliable range (sufficient for a booth footprint) |
| 3× tablets | Personal Layer software pre-installed, Bitcoin wallet pre-loaded, mounted on small tables, already Tailscale-connected |

**Two possible configurations, both valid depending on the trial:**
- **Brain PC on-site** — full rig travels to the venue.
- **Brain PC stays home, only tablets travel** — connects back over Tailscale via the mobile dish, exercising genuine Remote Mode. Preferred for early-development trials: nothing expensive or fragile leaves the house, and it deliberately stress-tests the Remote Mode path (see §6).

## 3. Software isolation — Home vs. Exhibition

The same physical Brain PC can run both Home AI and the exhibition demo without cross-contamination, via **two isolated Docker Compose stacks** rather than a disk partition or dual-boot:

- Separate container sets, separate Open-WebUI databases, separate data volume mounts per stack.
- Model weight files are shared underneath (Ollama stores them once; both stacks point at the same download) — no duplication needed, since model files carry no household-specific content.
- Switching is `docker compose down` on one stack, `up` on the other — seconds, no reboot.
- If stronger physical isolation is ever wanted, a second internal drive dedicated to the exhibition stack is the fallback — still one PC, one GPU, not a second machine purchase.

**Trade-off accepted:** if the actual household Brain PC leaves the house for an on-site trial, Home AI is offline for the duration. Confirmed acceptable for short periods.

## 4. Payment layer

- **Software:** self-hosted LNbits in front of Carl's own Lightning node/funding source — matches the project's self-hosted, no-third-party-custody standard elsewhere. LNbits' Paywall/LNURL-pay pattern (precedent: the `Boltar` extension — pay an invoice, receive a generated experience) is the closest existing template.
- **Onboarding is hybrid:**
  - **Visitor's own wallet** — if they have one on their phone, they send a few sats to the tablet's wallet directly.
  - **Facilitator-driven** — facilitator sends sats from their own wallet as part of the walkthrough, for visitors without a wallet or for pure demonstration purposes.
- **Payment is the session switch.** The tablet is already Tailscale-connected and pre-configured; receiving payment is simply the trigger that unlocks the timed AI session — not a separate provisioning step.
- **Countdown timer** on the AI session, visible to the visitor, tied to the amount paid.

## 5. Demo sequence (facilitator-narrated, three beats)

1. **Receive sats** — visitor (or facilitator, demonstrating) sends sats into an empty wallet on the tablet.
2. **Spend sats on AI compute** — wallet balance pays for a timed AI session; countdown timer starts.
3. **Log in via Tailscale** — the same login flow a household member uses remotely, demonstrating that this is the real system, not a simplified stand-in.

## 6. Network modes and known risks

Both Local and Remote Mode route through Tailscale identically — same client, same login, no visible difference to the user regardless of which physical link is underneath.

**Where this setup diverges from an ordinary remote household user, and what to expect:**

- **Shared-link contention.** A normal remote household user is alone on their own connection. Three tablets on one mobile dish share both the satellite uplink and the dish's own WiFi radio airtime — a failure mode the household spec never had to account for.
- **Two independent bottlenecks, not one.** `OLLAMA_NUM_PARALLEL=3` (GPU concurrency) and the mobile dish's shared bandwidth are separate limits. Either could be hit first; both feel identical to a visitor ("it's slow") but need different fixes.
- **Environmental fragility.** The dish needs continuous, unobstructed sky view — foot traffic, a marquee edge, a shadow at the wrong moment. A fixed household connection doesn't have this failure mode at all.
- **Deliberate test value:** running tablets on the mobile dish (never on the same network as the Brain PC) forces genuine Remote Mode with no risk of Tailscale silently preferring a local shortcut — incidentally a live rehearsal of the Tailscale-ACL/Ollama-queue marshalling gap flagged back in `spec-home-ai.md` §7, months before Phase 2 needs it to work.

## 7. Open decisions

- **Dedicated exhibition PC vs. the real household Brain PC.** Leaning toward dedicated for any live/on-site public trial (visitor traffic shouldn't mix with the household's real Open-WebUI instance or query logs); the Docker-stack approach in §3 makes either choice practical either way.
- **Indoor vs. outdoor venue.** Determines whether the fixed or mobile dish setup is even viable — needs confirming per trial location before the sky-view constraint becomes a last-minute problem.
- **Pricing per minute/block**, and wallet custody model (self-hosted node vs. a lighter hosted LNbits instance) for a short-run pop-up.
- **Real visitor payments vs. facilitator-only demonstration** — decides how much the wallet/payment reliability needs to be bulletproof on day one.
- **Load-testing protocol** — run all three tablets concurrently on the mobile dish under ordinary conversational load, to find out which bottleneck (GPU queue or dish bandwidth) actually shows up first in practice, rather than assuming.

## 8. Status

Concept and configuration decided at a design level; nothing built yet. Early-development stage — intended to be trialed in several different situations/venues before being treated as a fixed spec.

---

*v1 — first version of this document, compiled from working discussion. [date: 2026-09-17]*
