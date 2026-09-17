# Sales — Coop AI

**Draft v1** — 2026-09-17

A business-model companion to `phase2/spec-coop-ai.md`, not a technical spec. Captures a possible commercial offering: Coop AI hardware financed via monthly payments, positioned as an ordinary business expense rather than a SaaS subscription. Concept stage — not a current decision, not yet built.

---

## 1. The core idea

Offer a coop the Coop AI hardware (Brain PC + associated kit) financed over monthly payments, rather than requiring the full cost upfront. **Explicitly not a SaaS layer** — the software stays exactly as it is elsewhere in this project: free, open source, self-hosted, no recurring fee to access or use it. The monthly payment is financing a capital purchase, not renting ongoing access to a service.

## 2. Why this doesn't contradict the project's own premise

Worth stating plainly, since "monthly payments for the AI" can sound at first like the exact thing the household README argues against.

**The test that actually distinguishes them: what happens when payments stop.**

- **SaaS subscription** — missing a payment cuts off access immediately and permanently. Nothing was ever being built toward ownership, no matter how long or how much was paid. The dependency is indefinite by design.
- **Hire purchase (or equivalent finance-lease-to-own) hardware financing** — missing a payment risks repossession of the physical PC, a real and serious consequence, but every payment made up to that point built real equity in a real asset. Once the agreement completes, the coop owns the hardware outright, permanently, with nothing further anyone can take.

That's a finite dependency with an end date, not an indefinite one. It's the distinction that keeps this offering consistent with the project's founding premise rather than quietly reintroducing the failure mode it exists to opt out of.

## 3. Tax positioning

**The universal, always-true part:** purchasing (or hire-purchasing) computer equipment for a registered business or cooperative is a legitimate, ordinary deductible business expense — true in every EU member state and the UK, without exception. This isn't a jurisdiction-specific feature; it's a basic feature of having a corporate tax system at all.

**What is NOT universal, and must not be presented as if it were:** the specific mechanics — depreciation schedules, immediate-expensing allowances (e.g. the UK's Annual Investment Allowance, currently £1m/year, which has no single EU-wide equivalent), and how hire purchase specifically gets treated — are set nationally. Corporate/direct taxation is one of the few areas the EU has deliberately left unharmonised (it requires unanimous agreement among all member states to change at EU level, which in practice essentially never happens). VAT treatment of leasing/HP arrangements specifically has been the subject of real CJEU case law — whether an arrangement counts as a "supply of goods" (VAT due upfront) or a "supply of services" (VAT due per instalment) is genuinely litigated territory, not settled, and can depend on how the specific contract is structured.

**Practical consequence for the offering:** the pitch — "financing hardware over time is a legitimate business expense, not a hidden subscription" — can be stated once, universally, in any sales material. The actual financing paperwork cannot be a single template across countries. Every instance of this offering needs a "confirm with local tax/accounting advice" step built in per country, the same discipline already applied elsewhere in this project (e.g. the UPS's electrical compatibility) rather than presenting country-specific mechanics as if they were universal.

## 4. Why this fits Coop AI specifically, not Home AI

A household is not a legal entity and cannot enter a business hire purchase agreement or claim business capital allowances. A cooperative society is a real legal and tax entity — this offering only makes sense at that scale, which aligns naturally with Phase 2's existing structure rather than requiring any new scope.

## 5. Resilience — why a repossession scenario isn't catastrophic

If a coop did default and the hardware were repossessed, the loss is recoverable specifically because nothing in this architecture locks data to one physical GPU. The Node/Shared Library is a portable git repository; the model is an open-weight download; nothing proprietary is soldered to the hardware. Re-provision new hardware, restore from the same rotated backup discipline already built into Phase 1, and the coop is running again. This is the project's existing "no lock-in" design doing double duty — worth stating explicitly as a resilience property of the offering, not assumed.

## 6. Precedent — this is a known-working commercial pattern, not a novel risk

Red Hat and Canonical built real, substantial businesses on exactly this shape: the software stays free and open, and the commercial layer — hardware, support, financing — is sold on top of it, as a separate venture from the open project itself. This offering follows the same pattern: the technical architecture and its AGPL licensing don't change at all; what changes is that a business entity (Carl, or a purpose-built vehicle) takes on the role of HP provider — credit risk, asset title until final payment, the servicing relationship.

## 7. Open decisions

- **Who is the financing entity.** A separate legal vehicle from private-ai's own open licensing, since it carries credit risk and asset ownership obligations the open-source project itself doesn't and shouldn't.
- **HP term length**, ideally aligned with a sensible hardware refresh cycle (GPU-class hardware ages in relevance faster than most business equipment) rather than a generic multi-year term chosen for its own sake.
- **What's bundled** — hardware only, or hardware plus setup/support, and whether support is itself priced separately to keep the "no SaaS" line clean (a recurring support fee is a different, and more defensible, thing than a recurring access fee).
- **Per-country rollout sequencing** — given tax mechanics are national, this likely needs a first country validated properly (with real local advice) before being treated as a repeatable template elsewhere.

## 8. Status

Concept stage. Not a current decision — captures the reasoning so far for continuing this thread later.

---

*v1 — first version of this document, compiled from working discussion. [date: 2026-09-17]*
