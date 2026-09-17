# Community AI — Worked Example: Renewable Energy Community (CER)

**Draft v1** — 2026-09-17

A worked example for `phase2/spec-community-ai.md`, alongside the existing Victron VRM and weather-station Shape B examples. Captures a specific, well-fitted pairing: private-ai's Community AI Node paired with an Italian Comunità Energetica Rinnovabile (CER).

---

## 1. Why the fit works

A CER is a group of producers and consumers (households, small businesses, local authorities) sharing renewable energy — usually solar — on the same primary substation, formalised under Italian law (D.Lgs. 199/2021) and incentivised by GSE. As of 2026, the PNRR capital grant window has closed to new applications, but the 20-year GSE incentive tariff on shared energy (up to ~€120/MWh) remains open until end of 2027 or a 5GW national cap — meaning there's genuine, live formation activity right now, not a mature or settled market.

Setting up and running a CER requires real administrative competence — navigating the GSE portal, CACER rules, incentive calculation — that multiple industry sources describe as "not to be underestimated." That's exactly the kind of friction accumulated peer experience, made AI-queryable, meaningfully reduces for the next community forming. This isn't a speculative use case; it's a documented pain point the architecture already has the right shape for.

## 2. Two-tier membership, mapped onto the CER's own structure

CERs already formally distinguish **producers** (who host a generation installation) from **consumers** (who contribute a consumption profile and share in the benefit, with nothing to install). This maps directly onto two different access tiers — not an arbitrary split, but the CER's own membership structure doing the work:

- **Producer-members** → real Community AI Node accounts. Personal chat, sensor data flowing in, full AI query access to the Node's accumulated knowledge.
- **Consumer-members** (who may number in the dozens or hundreds) → the public-facing layer: Network Library + website mirror, read-only, no AI on that side — consistent with the existing Network Library Public Mirror rule.

## 3. Two data flows, two different mechanisms

Worth keeping distinct, since they use different existing pipelines:

- **Sensor data** (production curves, self-consumption ratios, battery state where applicable) — the **Shape B pattern**, identical in shape to the existing Victron VRM worked example: deterministic poll, markdown snapshot, `oikb` ingests, AI reads but never touches live.
- **Member experience** (GSE portal quirks, installer quality, how an incentive payment actually landed) — human-authored, pushed to the **Node Library** the same way a household member pushes to the Shared Library. Not automated, not Shape B.

## 4. Node sizing and federation

A Community AI Node already caps at up to 6 people sharing one Brain PC. Five rooftop-PV homeowners pooling one Brain PC is that structure exactly, applied at the scale CERs actually organise at — not a new pattern invented for this use case.

**A CER can, and likely will, contain several small producer-Nodes rather than one large centralised one** — five homeowners on one street pooling a Brain PC, another producer cluster elsewhere in the same CER pooling a second — each a genuine Node in its own right, federating whatever they choose to share up to the one common Network Library. This is Phase 2's federation model working at the actual granularity CERs organise at, rather than requiring one CER-wide Brain PC to be justified.

## 5. Cost, and why it scales with installation size rather than being universally negligible

- **Utility-scale wind/solar farm:** adding a Brain PC (~€2,362 at the Radiance tier) is genuinely a rounding error against total capital cost. The real saving isn't that the tech is free — it's that the farm's installation already has technicians on-site for electrical/network commissioning, so folding in one more low-power device avoids a second, separate mobilisation later.
- **Small rooftop CER cluster:** the same €2,362 is not negligible against a rooftop-scale budget. Split across 5 producer-members (optionally via the hire-purchase financing structure), it becomes a small enough monthly figure per household to stop being a real decision — the same logic as Coop AI financing generally, applied at CER-Node scale.

## 6. The public layer as an always-on social component

The Network Library plus website mirror compounds in a way a static project page or a WhatsApp group doesn't — today's posted production data, member experience, and FAQ answers make tomorrow's question easier to answer, the same durable-knowledge-base property that makes the Node itself useful. That's a genuine difference from conventional community-liaison tooling for a new installation, not just "social media for a power plant."

**Scope restriction, stated deliberately rather than left implicit:** this feature is built for and offered to genuinely community-owned, CER-structured installations. The identical feature — an always-on, transparent public layer showing a new installation's real data and community sentiment — can function very differently for a developer-led installation facing local opposition, where the same transparency layer risks operating as a social-license/PR tool smoothing over a project a community didn't actually choose. Given the project's own ethos is community control of infrastructure, not infrastructure imposed with a transparency veneer, this pairing is scoped to community-owned/CER installations specifically, not offered as a generic feature any developer could license regardless of how a project was sited or approved.

## 7. Status

Concept stage, worked through in detail. Not yet built — a candidate worked example for `phase2/spec-community-ai.md`, following Phase 1 validation, same sequencing as the rest of Phase 2.

---

*v1 — first version of this worked example, compiled from working discussion. [date: 2026-09-17]*
