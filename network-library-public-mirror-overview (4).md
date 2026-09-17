# Network Library Public Mirror — Project Overview

*(Formerly "Library AI." The public AI assistant was cut from scope — see §5. This is now a public, browsable/searchable/downloadable mirror of the Network Library.)*

---

## 1. Where This Sits in the Wider Architecture

Four tiers, increasing in scope:

- **Personal Library** — one person, own Android device, own Obsidian vault. Local-only. No AI attached, by design. Never synced or replicated anywhere.
- **Node Library** — up to 6 people sharing one node's AI (Open-WebUI). Private to that node. Never leaves it.
- **Network Library** — the federation-wide archive, full-pull replicated across trusted member nodes via Radicle (peer-to-peer git, no external host). No AI attached.
- **Network Library Public Mirror** *(this project)* — the public-tagged subset of the Network Library, exposed to the open internet.

**Node-tier AI has three separate visibility modes**, drawn by which tool is in use, not a group-wide default:
1. Private Obsidian writing — no AI, invisible to everyone.
2. Private 1:1 chat with the node's AI (Open-WebUI) — visible only to that individual.
3. AI use inside a shared Channel or live Notes doc — visible to whoever's in that space.

(Admin-level database access to stored chats via Open-WebUI's admin panel was raised and accepted as-is — not an open concern.)

**Governance (Archiver Protocol v2):** hardware-security-token identity → draft in Notes/Channels → formal Loomio proposal and vote → recorded Outcome → signed git push to self-hosted Forgejo, referencing the Loomio decision.

**Standing principle:** no open ports, nothing exposed to the public internet, private Tailscale network only — held everywhere *except* this project, which is the one deliberate exception.

---

## 2. What This Project Is

A public, browsable/searchable/downloadable mirror of the Network Library. File browsing and search — not conversational Q&A. No AI assistant runs on the public side (§5 explains why, and what replaces it).

---

## 3. Topology

```
Personal layer (Android devices, Obsidian)
        │
        ▼
┌─────────────── Private federation (Tailscale, no open ports) ───────────────┐
│                                                                               │
│   Node PC (×N) ◄──Radicle──► Node PC (×N) ◄──Radicle──► Relay box            │
│   AI hub + Node Library (private) + Network Library (replicated)             │
│                                              no governance identity,         │
│                                              push credential only            │
└──────────────────────────────────────────────────┬──────────────────────────┘
                                                     │ one-way push (outbound only)
                                                     ▼
                                    ┌───────────────────────────────┐
                                    │ VPS Gateway                    │
                                    │ Forgejo only (no Open-WebUI/GPU) │
                                    │ behind Cloudflare Tunnel       │
                                    │ + standard bot/DDoS protection │
                                    └───────────────┬─────────────────┘
                                                     ▼
                                                Internet
```

- **Relay box:** dedicated, minimal, low-power appliance. Normal Radicle node inside the private federation. Holds no governance identity (no hardware token, no Loomio access, no signing key). Only machine holding the write-only push credential to the VPS gateway.
- **Radicle:** confirmed compatible via `git-remote-rad`; interoperates with plain `git push`/`git pull`; invisible background replication only.

---

## 4. Hosting

Self-hosted Forgejo on a small, fixed-price VPS (~€4–10/month, Hetzner-class) — no cloud GPU, no third-party git platform. Compute needs are minimal without an AI workload; **storage and bandwidth are the real variables**, sized against actual archive content once known (§6).

Reused, not rebuilt: Forgejo (second instance, same tool as the private Archiver Forgejo), `oikb` (likely reusable for the relay box's push job), Forgejo's own file browsing/search as the public front end's base.

Genuinely new: the VPS, the relay box, a thin front-end skin over Forgejo's browse/search/download, standard Cloudflare protection (Tunnel + bot/DDoS).

---

## 5. Why No Public AI

Removing the AI assistant eliminated the GPU-class cloud requirement and the prompt-injection/content-safety exposure that comes with an open AI endpoint on the public internet.

**In its place — "bring your own AI":** any visitor can already point their own AI (Claude, ChatGPT, Perplexity, etc.) at the mirror's pages and ask questions directly. No central search system for the federation to build or fund. Three things make this work well:

1. `robots.txt` must welcome search engines and AI crawlers rather than block them.
2. Clean, stable, fetchable URLs per document (Forgejo's raw file/repo-browse links already suit this).
3. OCR discipline applied to everything published to the Network Library — a scanned image with no extracted text is nearly invisible to a fetch-based AI.

Side effect: copyright limits on how any AI can answer from fetched content (short paraphrase, pointing back to source) naturally drive visitors back to the actual mirror rather than substituting for it.

---

## 6. Open Questions

- Storage sizing — needs a real estimate of archive content volume.
- VPS provider/region.
- Bandwidth allowance vs. realistic public download volume.
- Relay box's push trigger mechanism and frequency (real-time vs. scheduled; whether `oikb` covers it).
- Shared subject-tag taxonomy across communities, now one gateway serves multiple contributors.
- Domain name and front-end design.
- Legal/rights re-confirmation at public-publish time.
- Takedown/retraction process — does the relay box's sync propagate removals?
- Who runs and pays for the shared gateway day to day.
- `robots.txt` policy — needs drafting.

---

*v6 source: 2026-09-14.*
