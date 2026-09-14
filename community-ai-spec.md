# Community AI — Specifications

## A Shared Digital Archive with AI Query Access, for a Community Association / Coop

**Companion documents:** [implementation checklist — to be created] · [The Archiver Protocol](archiver-protocol-v2.md), which defines identity, decision-making, and enforcement in full.

**Relationship to the household spec:** this reuses the household design's technical patterns (git-based storage, file conventions, `oikb` ingestion, Tailscale access) but is a different project, not a scale-up — the archive is the primary product here; AI chat is a supporting layer on top of it, not the point. The focus is not analogous to five individuals each wanting their own AI at the same time — it's collaborative: creating a digital archive, deciding together what belongs in it.

---

## 1. What This Is

A collaboratively-curated digital archive for a community association/coop — the actual deliverable is the archive itself: organized, searchable, version-controlled community material. An AI layer sits on top, letting anyone query the archive conversationally, but the archive has standalone value even without it.

## 2. Scale & Roles

- **6 Archivers** — fixed, known group, matching the Tailscale free "Personal" plan's 6-user ceiling exactly (same numeric fit as the household design's 5).
- Each Archiver has their own **personal Obsidian layer** — private notes, home-office-style working space where material is drafted and prepared before it's ready for group discussion. Same Android-first pattern as the household design, kept separate from the archive.
- One Archiver designated as the technical admin (equivalent to the household's single-admin role) — still a single point of operational responsibility; not distributed across the group, even though *decision-making* about content is.
- One fixed physical location (association office or clubhouse) hosts the node's PC — organizationally owned, same single-location model as the household design.

## 3. The Full Workflow — Personal Prep → Collaborative Workspace → Canonical Archive

The core lifecycle a piece of content moves through:

1. **Personal prep** — an Archiver drafts or gathers material in their own private Obsidian vault (§2). Nothing here is visible to anyone else yet.
2. **Collaborative workspace** — when ready to bring to the group, work moves into Open-WebUI's shared tools:
   - **Notes** — real-time collaborative document editing (built on Yjs/CRDT), the mutable draft layer.
   - **Channels** — real-time discussion, where the group talks through what's being proposed. (Beta feature in Open-WebUI, off by default, admin-enabled — treat with the same "check release notes before updating" caution already applied to Open-WebUI generally, if not more.)
3. **Canonical push** — once the group is satisfied, the finished document is pushed to the Node Library (§4) and becomes part of the AI's knowledge, the same discuss-before-push discipline the household spec established, extended to a group decision rather than an individual one. Because agreement happens *before* the push, the underlying git mechanism stays simple: a direct push of the finished document, no pull-request/approval-gate software needed.
- **Still worth deciding explicitly:** does every single addition need full group sign-off, or only contested/significant ones? A blanket rule is simple but slow; a threshold rule is faster but needs the threshold defined. **Governance mechanics for this are formally defined in the companion Archiver Protocol**, which replaced this spec's earlier bespoke voting design with Loomio as the sole decision/voting layer.

## 4. Node Library

Each community's own canonical Shared Library — bounded to that node's up to 6 named Archivers, shared among the node as a whole (not a separate private library per individual person). Never leaves its own node. Same git-based, bare-repo-on-the-server pattern as the household design.

## 5. Legal & Rights Considerations

- Archived material's rights status varies by source — unlike a household's own notes, this needs an actual policy, not an assumption.
- Confirmed as part of the Channel discussion (§3, step 2) rather than a separate compliance step.
- Worth deciding: does the association want a stated inclusion policy (e.g. "only material we have clear rights to, or that's explicitly community-authored") documented once, so it doesn't have to be re-litigated per item?

## 6. Hosting & Technical Architecture

- **Hosting:** one PC, one fixed physical location (§2). Physical security note: an office/clubhouse has different access patterns than a private home (cleaners, other members, opening hours) — worth a line on where the PC is physically secured within the space.
- **Node Library:** same git-based, bare-repo-on-the-server pattern as the household design.
- **Shared AI Brain:** same Ollama + Open-WebUI stack, same 16GB VRAM starting assumption — revisit if archive size or query volume differs meaningfully from household-scale usage.
- **Redis (new vs. the household spec):** required for Open-WebUI's Notes collaborative editing (Yjs/CRDT state) and Channels' real-time messaging. A genuine new dependency on the stack, not a big one, but worth naming rather than assuming it comes free.
- **Knowledge ingestion:** same `oikb` tool, pointed at the Node Library and, where applicable, the Network Library (§7).
- **Remote access:** same Tailscale free-tier + ACL pattern, sized for 6 named Archivers.
- **File conventions:** same plain-markdown, dated-filename, frontmatter discipline as the household spec.

## 7. Network Library — Federation-Scale, Shared Knowledge Only

What scales across nodes is *decided, published knowledge only* — the same distinction a physical reference library draws between its own archive and what the librarian has actively chosen to place in the public-domain section.

- **Network Library** — a separate, shared repository that any node's Archivers can deliberately publish specific canonical content *into*, once their own governance process (§3's discuss-before-push, extended one step further) has decided it's meant to be shared beyond their own community. Every member node's own `oikb` sync then pulls this second repository in as an additional source, alongside its own Node Library — feeding the same local AI, using the same local GPU. No node ever queries or borrows another node's compute.
- **Replication mechanism (decided, this draft): Radicle**, a peer-to-peer git layer — no external host, no GitHub, nodes gossip and replicate directly between each other's own AI PC hardware. This replaces the earlier placeholder of "a well-known shared git host (e.g. a private GitHub org the federation controls)," keeping all Network Library data on federation-owned hardware. Two open verification items: Radicle/Gitea tooling compatibility is untested, and it's a genuine new service running per node, not zero-new-infrastructure.
- **Scale:** genuinely unbounded relative to the 6-seat cap — the two numbers govern entirely different things. 6 is a live-compute-queue limit, specific to one node's own AI Brain. The Network Library has no queue at all; it can be pulled by any number of member communities without affecting any single node's processing capacity.
- **Not public internet.** "Scalable" means growing membership among known, vetted communities in the federation — not open to arbitrary members of the public. Still governed by an access-controlled repository, not a public-facing service; no open ports, no anonymous access, consistent with the household design's "no open ports" principle throughout. (The federation's public-facing exception to this principle is a separate, deliberately walled-off sub-project — see the Network Library Public Mirror handover.)
- **Governance:** the decision to publish something into the Network Library belongs to whoever already governs the originating node's own Library — the same discipline as the existing discuss-before-push rule, applied one level further outward.

---

*Draft v6 — supersedes v5. Replaces the GitHub-org placeholder in §7 with Radicle, a peer-to-peer git replication layer — keeps all Network Library data on federation-owned hardware, no external host. Corrects the earlier "no new networking layer needed" assumption. [date: 2026-09-14]*
