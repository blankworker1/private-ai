# Site — private-ai.tech

**Status: planning.** The site goes live only after the Phase 1 household trial (see `DECISIONS.md`, Go-to-market).

**Purpose:** the home base. Social platforms carry the campaign episodes; this site explains, links to the hardware, offers the software, and describes the setup service. Every episode points here.

---

## Principles

- **No tracking.** No analytics, no pixels, no cookie banners because there are no cookies to ask about. A visitor checking for trackers should find none.
- **No tracking embeds.** Episodes are hosted as files or linked out, not embedded as players that track visitors.
- **Honest about limits.** The site says plainly what private-ai can't do: smaller models than the frontier cloud services, one GPU shared by the household, no vendor support line.
- **Mirrors the repo.** Specs and decisions live in the repo; the site summarises them in plain language and links back.

---

## Planned pages

| Page | Content |
|---|---|
| **Home** | The core message, the latest episode, the three tiers at a glance |
| **Why** | Plain-language version of `Comparison.md` and "Someone Else's Story": the book, the cloud, the AI |
| **How it works** | The three layers, the four levers (`model-selection.md`), the off switch |
| **Get the hardware** | The tiers, with links to suppliers (affiliate links where available, clearly disclosed) |
| **Get the software** | Download private-ai's own layer: install scripts, config, Library template, docs. Upstream software comes from its official sources. |
| **Setup service** | Summary of `service.md`, including the access protocol |
| **Episodes** | The campaign archive, each with its credits card |
| **Repo** | Link to the GitHub repository |

---

## Hardware tiers

| Tier | Hardware | Approx. price | For |
|---|---|---|---|
| **Micro Brain** | Seeed reComputer J3011 (Jetson Orin Nano 8GB) | ~€650 | Entry level, small model, one household or a demo seat |
| **Brain PC** | Radiance Systems CoreAI 16 (RTX 5060 Ti 16GB) + Turnkey AI Pack | ~€2,360 (Carl's configuration, Italy delivery) | A five-person household |
| **Coop AI** | Phase 2 node | Later, possibly by hire purchase | Communities and co-ops |

Prices are as quoted to this project and will change; the site should show the supplier's current price, not ours.

---

## To do

- [ ] Ask Radiance Systems and reichelt whether they offer affiliate or referral programmes
- [ ] Choose hosting that fits the principles (static site, no third-party scripts)
- [ ] Decide what the "Get the software" download actually is: release archive, install script, or repo link
- [ ] Affiliate disclosure wording
- [ ] Languages: English, Italian, Welsh?
