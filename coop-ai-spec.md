# Coop AI — Spec Draft v1
## Making a Cooperative Corporate Structure AI-Ready, Built on the Same Household/Community Architecture

**Companion document:** [The Archiver Protocol](archiver-protocol-v2.md) — defines identity, decision-making, and enforcement in full; this spec doesn't repeat that mechanism, it applies it.

**Relationship to the other specs:** same structures, same tools as the household and Community AI designs — but the aim here is neither a private household platform nor a collaboratively-curated archive. It's integrating standard cooperative governance, decision-making, and record-keeping into the same self-hosted AI stack, so a coop's formal corporate structure becomes AI-queryable.

---

## 1. What This Is

A 6-person cooperative board's governance record — resolutions, votes, minutes, outcomes — made conversationally queryable by a self-hosted AI, with the actual decision-making running on mature, purpose-built cooperative governance software rather than anything bespoke.

## 2. Scale & Roles

- **A board of 6** — the same numeric fit as the household's 5 and the Community AI archive's 6 Archivers, sitting exactly at the Tailscale free "Personal" plan's user ceiling.
- Each board member has their own **personal Obsidian layer**, same Android-first pattern as the other two specs — private notes and prep, kept separate from anything formal.
- One member designated as the technical admin — a single point of operational responsibility, distinct from *decision-making*, which stays one member, one vote throughout.

## 3. Why Not Build Governance From Scratch

Before drafting this spec, the honest question was whether the Archiver Protocol's own bespoke voting mechanism (required approvals on git, status-frontmatter conventions) should simply be reused here. It shouldn't be: **structured decision-making, formal voting, and a permanent governance record are a solved, mature, coop-native problem.**

**Loomio** (self-hosted, open-source, AGPL) is the genuine article, not an approximation — worker-cooperative-built, in active use across 100+ countries, purpose-built specifically for formal proposals, structured consent voting, threaded discussion, and a permanent decision archive built explicitly around **ICA Principle 2 (one member, one vote)** — the actual cooperative governance principle, not a generic chat feature.

**What Loomio genuinely doesn't have** — and what this project still supplies: no self-hosted AI layer able to query the governance record conversationally, no live collaborative document co-editing, and it isn't built on the git-based, personally-portable storage model the rest of this project uses. That's this spec's actual job: wire Loomio's governance record into the same AI/knowledge-base pattern already built for the household and Community AI use cases.

## 4. The Governance Workflow

Identity, decision-making, and enforcement mechanics are defined in full in the companion **Archiver Protocol v2** — this section states how they apply to a coop board specifically:

1. **Personal prep** — a board member drafts or gathers material in their own private Obsidian vault. Nothing here is visible to anyone else yet.
2. **Discuss/draft** — moves into Open-WebUI's Notes (live collaborative drafting) and/or Channels (informal discussion) ahead of anything formal.
3. **Formal proposal and vote** — a Loomio proposal is created, linking to the Notes document; the board votes under one-member-one-vote.
4. **Recorded Outcome** — closing a Loomio poll doesn't execute anything automatically; a human is prompted to write an Outcome statement summarizing what happened and what's next. This matches the project's "not trustless execution, a human still acts" principle throughout.
5. **Signed git push** — the designated board member executes the push to the self-hosted Gitea instance, with the commit message referencing the Loomio poll's ID/URL. Git-level enforcement is identity-verification only (SSH-signed via the same hardware token used for Loomio/Tailscale auth) — no separate approval-count gate, since Loomio's poll is already the governance decision.
6. **AI ingestion** — Loomio's decision record is pulled into `oikb` as a feed, the same mechanism already used for the household's Shared Library and the Community AI Node/Network Libraries. The board (or, subject to access policy, members generally) can then query governance history conversationally: what was decided, when, and why.

## 5. Hosting & Technical Architecture

- **Hosting:** one PC, one fixed physical location — same single-location model as the household and Community AI designs.
- **Loomio:** self-hosted via the team's own official `loomio-deploy` repository (Docker Compose) — genuine, maintained infrastructure, not a workaround. The default deploy guide assumes a public-facing setup (public IP, domain, Let's Encrypt); this project instead runs Loomio privately, reachable only by the 6 board members via Tailscale, using Tailscale's own HTTPS certificates for tailnet-only services — consistent with the "no open ports" principle held everywhere else in this project.
- **Shared AI Brain:** same Ollama + Open-WebUI stack as the household and Community AI specs.
- **Redis:** required for Open-WebUI's Notes (Yjs/CRDT) and Channels, same as the Community AI spec.
- **Knowledge ingestion:** `oikb`, fed from Loomio's decision record via its documented API (`api/b1/polls`) in addition to any Shared Library material the board maintains.
- **Remote access:** Tailscale free-tier + ACL, sized for 6 named board members.
- **Everyday toolset:** three separate surfaces — Obsidian (personal notes), Open-WebUI (AI chat/Notes/Channels), Loomio (formal decisions) — no merged app. Loomio runs as an installable web app via "Add to Home Screen," the same pattern as Open-WebUI, with working browser push notifications for votes needing attention. (Loomio's team is building native iOS/Android apps as a next step; the PWA is the current, reliable answer.)

## 6. What This Deliberately Is Not

*(Inherited directly from the Archiver Protocol — restated here because it matters most in a formal corporate-governance context.)*

- **Not a blockchain** — no consensus mechanism, no distributed trust-minimization; the board already knows and trusts each other, and the self-hosted infrastructure is trusted infrastructure the coop controls.
- **Not a token-weighted DAO** — one member, one vote, matching ICA Principle 2, closer in spirit to a cooperative's own bylaws than to crypto-DAO governance.
- **Not trustless execution** — a human still runs the actual push once a Loomio outcome is recorded; nothing executes itself automatically.

## 7. Open Questions

- Whether the AI-queryable governance record should be accessible to all cooperative members (not just the 6 board members), and if so, under what access policy.
- Formal legal/admissibility status of a Loomio-recorded Outcome as an official board minute, in whatever jurisdiction the cooperative is registered in — not yet researched.
- Whether Loomio's decision record needs any redaction or delay before AI ingestion (e.g. sensitive personnel or financial votes).

---

*Draft v1 — first standalone version of this spec. Synthesizes the Coop AI reframing discussion, which had previously been folded into the Community AI spec and the Archiver Protocol without its own document. [date: 2026-09-14]*
