# Coop AI — Specifications

## A Single-Node Community Cooperative: AI, Data Custody and Governance for One Comune

**Draft v2** — 2026-10-19 · Pilot: **Bosa™** (Comune di Bosa, Sardinia)

**Companion documents:** [The Archiver Protocol](archiver-protocol.md) (identity, Loomio governance, signed pushes) · [Data Template](data-template.md) (Shape B ingestion, rollups) · [Sales — Coop AI](sales-coop-ai.md) (hire-purchase hardware financing) · [CER worked example](cer-community-ai.md) (partly superseded — see §15)

**Relationship to the other specs:** same stack as Home AI and Community AI (Ollama + Open-WebUI, Forgejo, `oikb`, Loomio). What changes in v2 is the *frame*: Coop AI is no longer only a board's AI-queryable governance record. It is the AI and data layer of a **cooperativa di comunità** — one node, one comune, one membership — where members bring their own data in by consent, the co-op holds it and never shares or sells it, and the AI turns it back into answers for members and for the community.

---

## 0. What Changed From v1

| v1 (2026-09-14) | v2 (this draft) |
|---|---|
| 6-person board as the whole user base | Board + full membership (hundreds of residents) |
| Tailscale free tier, 6-seat ceiling | Headscale (self-hosted), no seat ceiling — per `DECISIONS.md` |
| Governance record only | Governance record **plus** a data co-op layer (§6) |
| Federation implied via Community AI's Network Library | **Single node.** Federation deferred behind an assembly-vote clause (§12) |
| No defined catchment | Comune di Bosa residents only (§4) |
| No data-sharing rule | Entrenched **no-share, no-sale** rule (§7) |
| No external partner | Modolo CER as first partner, contractual only (§10) |
| — | Youth dote for the 18–23 cohort (§4.3), outreach workshops and Bosa™ boards (§11) |
| Obsidian Personal Layer | GitJournal Personal Layer (per `notes.md` #4) |

---

## 1. What This Is

A community cooperative running three mutual activities on one self-hosted machine in Bosa:

1. **Energy** — promoting and coordinating members' participation in the existing Modolo CER; roof contributions via *diritto di superficie* (see `bosa-pilot` working notes). Not a new CER.
2. **Data custody** — members' activity-related data (energy, local environment, community knowledge, governance) held on co-op hardware under consent, never shared, never sold.
3. **AI service** — a local model answering members' questions from their own data, the community's aggregates, and the co-op's governance record.

Governance across all three runs on Loomio (one member, one vote) with the outcome-then-signed-push discipline from the Archiver Protocol.

**Positioning in one line:** *a cooperative that owns the community's data by holding what members choose to contribute — and whose founding rule is that the data is never for sale.*

**Why now:** data cooperatives are recognised in EU law (Data Governance Act, Reg. 2022/868) and there is a mature Italian research effort (University of Bologna third-mission project with Legacoop's Fondazione PICO, including a model data-co-op statuto in *EU Data Cooperatives*, Bravo ed., Giappichelli 2024, open access). No operating data cooperative was found in Sardinia, nor a clearly operating one in Italy. Bosa™ would plausibly be among the first.

---

## 2. Founding Principles (Settled)

- **Single node.** One machine, one location, one membership. No data link to any other node.
- **Growth by replication, not networking.** The model spreads by other comuni founding their *own* co-op from the same statuto and spec templates — each sovereign, none dependent on another. Same principle as Sardigna™: uniformity through shared templates, never ownership links.
- **Defined catchment.** Membership is open to residents of the Comune di Bosa. The comune is the co-op's *comunità di riferimento*.
- **Never shared, never sold.** Member data and anything derived from it stays on co-op hardware (§7).
- **Consent in, individual control out.** Data arrives through each member's own consent and action; every member can export or delete their data at any time.
- **Scoped, not total.** The co-op holds only data tied to its own activities — not members' whole digital lives (§6.1).
- **Partners by contract, not structure.** The CER is the first partner. No ownership links in either direction.
- **No open ports.** Unchanged from every other spec. The outreach boards (§11.2) are offline and never connect to the co-op's systems.
- **Not trustless execution.** A human still acts after every recorded decision. Unchanged.
- **Pilot to prove the model.** Everything here is scoped to proving the model in Bosa before any replication.

---

## 3. Legal & Corporate Structure

*Everything in this section is a working position for the notaio and cooperative-law consultant to confirm. Not legal advice.*

### 3.1 Vehicle

- **One cooperativa di comunità** running all three activities. Sardinia's regional law on cooperative di comunità defines them as cooperatives explicitly aimed at benefiting a community their members belong to or choose as their own, with the reference community being the comune(s) where they operate, and allows them to take a **mixed** form (production-and-labour, support, user, social). A user-cooperative element (members using the co-op's services) fits the data and AI activities; the energy activity fits alongside.
- **Ring-fencing inside the one vehicle:** separate accounts and a separate risk register for the data/AI activity (a data breach is a different liability class from energy contracts). Roof and land assets keep the *atto di destinazione* protection and the no-mortgage rule already planned for Sardigna™, so an AI-side problem cannot reach them.
- **Island tier stays separate.** The Sardigna Srl-PMI tier is unaffected. No parent–subsidiary links.
- **Possible later split:** if Trade AI's external inference market ever grows large, the AI service could move to its own co-op for a cleaner liability shield. Not for the pilot.

### 3.2 Oggetto sociale — the data activity as a member service

The statuto describes the data activity as a service to members: **custody, processing and AI services for the members' benefit**, with the no-share/no-sale rule (§7) written into the purpose itself rather than kept as policy. This supports the co-op's mutual character and makes the rule hard to drift away from.

### 3.3 Data Governance Act position

The DGA's heavier regime (notification, structural separation, purpose restrictions) applies to **data intermediation services** — brokering data between holders and external users. Because the co-op uses member data only internally, to serve members, it is expected to sit **outside** that regime. To confirm with counsel. If the co-op ever intermediated, §7 already forbids it; changing that would require the entrenched-amendment procedure.

### 3.4 Remote voting

The civil code allows the statuto to provide for voting by correspondence or electronic means (art. 2538 c.c.). This is what lets a Loomio poll count as a valid assembly vote rather than an indication. Exact wording to confirm with the notaio.

---

## 4. Membership

### 4.1 Admission

- **Criterion: residency in the Comune di Bosa** (anagrafe residence). Admission requirements must be non-discriminatory and consistent with the mutual purpose (art. 2527 c.c.); residency is the cleanest fit with a cooperativa di comunità.
- **Birthplace is not an admission criterion** at this tier (that logic stays at the Sardigna island tier).
- **Admission is by application** — the directors resolve on each application (art. 2528 c.c.); refusals must be reasoned and can be appealed to the assembly. The co-op stays open-door: any eligible resident can apply at any time. Nobody is enrolled automatically.

### 4.2 Share classes

| Class | Who | Terms |
|---|---|---|
| **Resident / membership share** | Any Bosa resident | Nominal quota of at least €25 (art. 2525 c.c. minimum) plus an annual membership fee. Lapses on non-payment or loss of residency. Non-transferable. Gives full access to all three activities and one vote. |
| **Roof Contribution sub-class** | Residents contributing roof capacity | Time-bound, lighter than the land-based Contribution class; diritto di superficie; homeowner keeps the panels, production output transfers. Exempt from the annual fee. |

**Data is never capital.** There is no "data contribution" share class. Consent to process personal data is always revocable, so data cannot serve as stable conferimento. Members contribute data under consent; the co-op holds custody, not ownership.

**One head, one vote** across all classes (art. 2538 c.c.).

### 4.3 Youth dote — the 18–23 cohort

The Sardigna-style counterpoint to a billionaire handing out shares in a company: Bosa's young residents get a **vote and a service in a cooperative they can actually govern**, not an economic claim without control.

- **Mechanism:** a separate **dote fund** (association or foundation) pre-pays the €25 quota — and optionally the first year's fee — for each eligible resident who applies. The co-op itself never enrols anyone; the fund simply pays on the applicant's behalf.
- **The age logic lives only in the fund's rules, never in the co-op's admission rules.** The co-op's door stays open to every resident on equal terms; the fund decides whose quota is pre-paid. This keeps the co-op's criteria clean under art. 2527.
- **Size (estimate from ISTAT data at 1 Jan 2025):** roughly **400 residents aged 18–23**, and about **60–65 reaching 18 each year**. Founding-cohort cost ≈ €10,000; ongoing ≈ €1,600/year. The cost is minor — governance and participation are the real questions.
- **Optional apprentice category:** the statuto may admit new members into a special category for training/integration for up to five years, capped at one third of total members (art. 2527 c.3). A natural fit for 18–23s, but at Bosa's scale (~7,300 residents) a large uptake could hit the one-third cap. Decide between admitting the cohort as full members from the start, or using the category with an explicit growth plan.
- **Registered vs present:** many 18–23s are registered in Bosa but study or work elsewhere. Under an anagrafe-residency criterion they qualify. Treat this as a feature: it keeps students tied to the community while away.
- **Funding the dote** from donors or from the island-tier Srl is a gift, not an ownership link; confirm with the notaio that it doesn't blur the clean separation between entities.

### 4.4 Reaching members — lawfully

- **No purchased contact lists.** Consent chains behind broker lists are weak, the buyer carries the GDPR liability, and in a town of ~7,300 an age-and-location list is near-identifying. It would also contradict the co-op's own message.
- **No anagrafe extraction.** The Comune cannot hand over registry lists for recruitment. The only lawful "list" is the one that builds itself from applications. Ask the Comune's DPO whether the Comune could itself send an institutional letter under a convenzione, so the data never leaves the town hall — do not assume it can.
- **Referral, not collection.** Members pass on an invitation (card, QR code, word of mouth); the newcomer submits their own details with a proper privacy notice. Any incentive is paid per **admitted member**, never per name handed in, kept modest and non-cumulative. Paying residents to gather other people's contact details is ruled out: it would make the co-op controller of data about people who never approached it, and turn the community into a bounty system.
- **Platform-side ad targeting** (age band + radius around Bosa, optionally wider to reach students in Sassari and Cagliari): the platform delivers the ad without the co-op ever seeing who anyone is. Check reach estimates first — the audience may be in the hundreds.
- **Places people already are:** secondary schools (incoming 18-year-olds), sports clubs, the English Breakfast table, the outreach workshops (§11.1) and the Bosa™ boards (§11.2).

### 4.5 Exit

- Membership lapses on loss of residency or non-payment of the annual fee; the nominal quota is reimbursed to the member (including a dote-funded quota — accepted, or restructure the dote if it should return to the fund).
- On exit, the member's data is exported to them on request and deleted from co-op systems. Aggregates already computed and published (§7.4) are not unwound, since they contain no individual data.

---

## 5. Roles & Scale

- **Members (hundreds):** simple login (username/password + optional passkey) to Open-WebUI and Loomio over Headscale. No hardware token required.
- **Board / Archivers:** hardware security tokens (YubiKey-class, two each), in-person enrolment, signed Forgejo commits — the Archiver Protocol unchanged, applied to the board only.
- **Technical admin:** one board member, a single point of operational responsibility — distinct from decision-making, which stays one member, one vote.
- **Data steward:** a named board role responsible for consent records, export/deletion requests and the aggregation threshold (§7.4).
- **Board-rounds role:** the weekly walk to update the Bosa™ boards (§11.2) — a natural role for a member of the youth cohort.
- **Remote access:** **Headscale** (self-hosted) per `DECISIONS.md` — removes the 6-seat ceiling. The real constraint becomes the GPU queue (`OLLAMA_NUM_PARALLEL=1`, one request at a time). At hundreds of members, queue behaviour at peak times must be tested in the pilot before promising response times (see §14).

---

## 6. The Data Co-op Layer

### 6.1 Scope — what the co-op holds, and what it doesn't

**In scope** — data tied to the co-op's own activities:

| Type | Source | Who sees it |
|---|---|---|
| Household energy | Member's PV production, consumption, battery state; member's own meter data | That member only |
| Collective energy | Aggregates computed from the above (§6.4) | All members |
| Local environment | Co-op weather station and sensors | All members |
| Community knowledge | How-tos, local history, documents members choose to add | All members |
| Governance record | Loomio decisions and outcomes, minutes, accounts | All members (subject to §8.3 redaction) |

**Out of scope** — banking, social media, health, general utilities and members' personal notes. The test is **purpose, not origin**: a meter reading from the grid operator is in scope because it serves the energy activity; a bank statement is out because it serves none.

This scoping is not only a design choice. GDPR purpose limitation and data minimisation require it. It also limits breach liability and avoids turning one small server into a honeypot of everyone's full digital life. Members who want control over their whole profile are helped to keep it **themselves**, on their own devices (§11.1) — the co-op teaches and equips; it does not hold.

### 6.2 How data arrives — consent first

- **Every inbound flow is authorised by the individual member.** No bulk data agreement with the CER, the grid operator, or anyone else.
- **Meter data:** each member downloads or authorises release of their own consumption data (e.g. via ARERA's Portale Consumi) and contributes it.
- **Inverter / battery data:** the Brain PC cannot reach into members' home networks (no open ports, no inbound connections). Two intake options:
  - **Pilot:** periodic member upload of the inverter's own export, converted to the Data Template schema by a deterministic script on intake.
  - **Later:** a small member-side collector (Micro Brain PC / Power Control Node class, or a Raspberry Pi) polls the inverter locally — Shape B — and pushes the dated snapshot **outbound** to the co-op over Headscale. The co-op never polls a member's home.
- **Co-op sensors** (weather station etc.): standard Shape B on the co-op's own network.
- **Human-authored knowledge:** pushed to the Node Library through the normal discuss-before-push process.
- **Consent records** are kept per member, per data type, with date and scope; revocation is honoured by deletion and exclusion from future rollups.

### 6.3 What the co-op does with it

1. **Custody** — co-op-owned hardware in Bosa; encryption at rest; rotated air-gapped backup (same discipline as `spec-home-ai.md` §4a); export and deletion on request.
2. **Personal answers** — "when should I run the washing machine?", "what did my roof produce this month versus last year?" — answered only from that member's own data.
3. **Community answers** — the town's energy balance, when there's surplus, how much of its own production it uses. Feeds collective decisions, including later uses such as allocating surplus to mining or inference (`miner-power-control`, Trade AI).
4. **Institutional memory** — "what did we decide about X, and why?", answered from the governance record. This is what makes the co-op legible to new members, including the youth cohort.
5. **Collective voice** — with the CER, the GSE or the Comune, the co-op negotiates using its own **published aggregates**, never by handing over raw data.

### 6.4 Partitioning — the main technical change from Home AI

In Home AI the Shared Library is visible to the whole household. In Coop AI it cannot be:

- **Per-member knowledge collections.** Each member's household data is ingested into a collection visible only to that member's account. Shared material (aggregates, environment, community knowledge, governance) sits in co-op-wide collections. **To verify in the pilot:** Open-WebUI's per-user/per-group knowledge permissions enforce this reliably at retrieval time, and an admin-visible chat log policy is acceptable to members (v1 accepted admin visibility for a 6-person board; at membership scale this needs an explicit, published policy).
- **Aggregates are computed by deterministic rollup scripts** (Data Template §7), never by the AI reading across members. The AI only ever sees the finished aggregate file.
- **Aggregation threshold:** no aggregate is computed or shown for fewer than a set number of households (proposed minimum: 5), so that in a small town no figure can be traced back to one home. Owned by the data steward.

### 6.5 Mutual exchange

The data and AI activities give every member — including those with no roof — something real to use. This addresses the risk of passive members (gifted shares that never take part in the mutual exchange), which matters for the co-op's mutual character.

---

## 7. The No-Share, No-Sale Rule

### 7.1 The clause (intent, for drafting with the notaio)

> The cooperative shall not sell, license, share, transfer or otherwise make available to any third party any data contributed by members, or any artefact derived from it, except as required by law. Member data shall be processed solely to provide services to members.

### 7.2 Two locks

- **Collective lock — entrenchment.** Under one head, one vote, an ordinary majority could vote the ban out. The clause is therefore placed in the statuto with a high amendment threshold (large supermajority or unanimity — to set with the notaio).
- **Individual lock — GDPR.** Members consent to their data being used to provide services *to members*. Purpose limitation means any sale or sharing would need each member's fresh, individual consent. No majority vote can override that. The rule rests on both collective governance and individual rights.

### 7.3 What counts as sharing

- **Raw data**, obviously.
- **Derived artefacts:** fine-tuned model weights, LoRA adapters, embeddings, vector indexes, knowledge-base exports. A model trained or tuned on member data can leak that data; publishing or selling the model is effectively sharing the data.
- **Member data leaving co-op hardware** for any reason other than the member's own export or a legal obligation.
- **Answers as a side door:** if Trade AI later sells inference externally, that inference runs on **base models with no member knowledge base attached**. Selling answers must never become a way around the no-sale rule.

### 7.4 What is permitted

- **Legal carve-outs, named explicitly:** mandatory CER/GSE reporting, tax and audit obligations, court orders. Naming them keeps the ban credible.
- **Published anonymised aggregates** (e.g. "the co-op produced X MWh this year"), subject to the aggregation threshold (§6.4). Part of what makes the experiment visible.
- **Code and model-free software** — the co-op's scripts and configuration are open (AGPL, as the rest of private-ai) and can travel freely. Only data and data-derived artefacts are locked.
- **The member's own export** of their own data, at any time.

---

## 8. Governance

### 8.1 Two levels

- **Member assembly** — all members, one vote each. Loomio polls count as assembly votes where the statuto provides for electronic voting (§3.4). Covers statuto changes, annual accounts, and anything the statuto reserves to the assembly — including any future federation proposal (§12).
- **Board** — runs the co-op day to day, using the Archiver Protocol in full (hardware tokens, signed pushes).

### 8.2 Workflow (from v1 §4, extended)

1. **Personal prep** — in the member's own Personal Layer (GitJournal, Android-first). Nothing visible to anyone else.
2. **Discuss/draft** — Open-WebUI Notes (live drafting) and Channels (discussion).
3. **Formal proposal and vote** — a Loomio proposal linking the Notes document; board or assembly votes, one member, one vote.
4. **Recorded Outcome** — a human writes the Outcome statement. Nothing executes automatically.
5. **Signed git push** — a board member pushes to the self-hosted Forgejo instance, commit message referencing the Loomio poll ID/URL; SSH-signed with the hardware token.
6. **AI ingestion** — Loomio's decision record (`api/b1/polls`) feeds `oikb`; members can query governance history conversationally.

### 8.3 Access to the governance record (resolves v1 open question)

- **Default: the whole governance record is visible to all members.** In a co-op where the AI is meant to make the institution legible, members are the audience.
- **Redaction before ingestion** for matters involving identifiable individuals: personnel, individual member disputes, admission refusals, arrears. These are summarised in the Outcome without personal detail; the full record stays with the board.

---

## 9. Hosting & Technical Architecture

- **One PC in Bosa**, physically secured, in a location the co-op controls. Physical-security note from the Community AI spec applies (shared premises have cleaners, visitors and opening hours).
- **Full stack on one machine** (per `notes.md` #1): Ollama, Open-WebUI, Redis, Forgejo, Loomio, Headscale coordination server, `oikb`, rollup and intake scripts. Size system RAM and disk for all of these, not just the GPU (per `notes.md` #2).
- **Shared AI Brain:** Ollama + Open-WebUI, 16GB-VRAM starting assumption — revisit after queue testing at membership scale.
- **Node Library:** Forgejo bare repo, plus per-member collections (§6.4).
- **Remote access:** Headscale (self-hosted), ACL by role (member / board / admin). No open ports.
- **Power:** UPS as Phase 1; later the co-op's own PV and battery — a co-op AI running on member-contributed sun is the obvious demonstration of the model.
- **Backup:** rotated air-gapped external drive; encrypted; restore tested.
- **Hardware financing:** hire purchase per `sales-coop-ai.md`. The co-op is a legal entity and can enter the agreement; a household could not. Resilience holds: nothing locks data to one GPU, so repossession is recoverable from backup.
- **Revenue model:** membership fees and the energy activity. The data activity earns nothing directly — and, by §7, never will. That is part of the pitch: *the co-op earns from serving members, never from selling them.*

---

## 10. Partners — the CER First

- **Modolo CER** (neighbouring comune, Unione di Comuni Planargia) is the first partner. Bosa™ acts as **promoter and participant**, onboarding Bosa homeowners into the existing CER rather than forming a new one.
- **Contractual, not structural.** The CER is a separate legal entity with its own rules; no ownership links either way. "Limited to Bosa" applies to the co-op's membership and data — not to every contract it signs.
- **Data from the CER arrives through members, not through the CER.** Each member authorises their own meter and production data (§6.2). No bulk data-sharing agreement — this keeps consent individual and the custody chain clean.
- **Outbound to the CER:** only what members have mandated or the law requires, plus published aggregates (§7.4).
- **The Comune:** positioned as complementary, not competing. A convenzione (the Region provides model agreements between cooperative di comunità and public administrations) can cover outreach support and a slot for Comune notices on the Bosa™ boards — never data.

---

## 11. Outreach

### 11.1 Personal data awareness workshops

A second entry point alongside the roof/PV offer — the roof offer reaches property owners; the workshops reach everyone else, including the youth cohort.

| # | Workshop | Leads into |
|---|---|---|
| 1 | **"What do they know about you?"** — each participant requests their own data from one platform they use (access/portability rights, GDPR Art. 15 and 20) and looks through it | Awareness |
| 2 | **Your utilities and energy** — download and read your own meter data | CER, roof offer |
| 3 | **Your money trail** — what banks and payment apps record | Slow Money, *The Test* |
| 4 | **Keeping it yourself** — store what you downloaded on your own device | Personal Layer |
| 5 | **The community version** — a co-op that never sells data, and how to join | Membership |

- **AI content:** workshop guides, request templates (Art. 15/20) and explanations go into the Node Library in Italian, so members can ask follow-up questions ("how do I request my data from X?", "what does this field in my export mean?").
- **Informational, not legal advice:** the AI explains rights and processes; it does not advise on individual disputes. Stated on the guides and in the AI's system prompt.
- **Venue:** the English Breakfast table as a regular slot; schools for the incoming 18s.

### 11.2 Bosa™ boards — SUNDIAL's second life

After the **SUNDIAL · La Meridiana** exhibition (PVA, summer 2027) completes its bounded run, its panels can become semi-permanent, hyper-local digital notice boards — a branded Bosa™ service.

- **Art and service stay separate.** SUNDIAL keeps its defined end (bounded run, canonical screen print). The boards are a post-exhibition conversion, under a licence from PVA to the co-op (keeps the artwork's origin clear) — or a purchase.
- **Offline by design:** each panel's ESP32 serves pages over its own access point. No internet, no app, no account. **Connection logging off by design** — an ESP32 AP can see device identifiers (MAC addresses); the boards must not record them. A notice board that sees no one is the no-collection principle made physical.
- **Open when the sun is up.** Each panel powers only its own page server; no battery, so each board's hours follow the sun at its site.
- **Updated by walking.** Content is uploaded locally over Wi-Fi on a weekly round of the trail (the reverse of SUNDIAL's planned admin protocol). The boards never connect to the co-op's systems; content is copied by hand from the co-op's published material.
- **Content:** co-op notices and Loomio outcomes (public summaries only), workshop guides, the panel's own live PV dashboard with community energy aggregates beside it, events (English Breakfast, Slow Theatre), and a slot for the Comune's notices.
- **To check early:** wall owners' consent; Comune and heritage authorisation in the centro storico (the most likely obstacle); outdoor weatherproofing for multi-year service.

---

## 12. Federation — Deferred

- **Removed from the pilot.** No Network Library, no Radicle replication, no relay box, no Library AI gateway in Bosa™. All remain documented in `/phase2` as later options.
- **Statuto clause:** any future link to another cooperative requires an **assembly vote**, and **can never include member data** or data-derived artefacts (§7.3). Code, configuration and base models may travel.
- **Cross-community exchange, if ever wanted:** published how-to documents can be exchanged manually between co-ops, as documents, through each co-op's own discuss-before-push process.
- **Growth model:** replication by template (§2). If Modolo, Tresnuraghes or any other comune wants Coop AI, it founds its own co-op from the same statuto and spec templates.
- **Trade AI's collective offer** (a community of nodes offering inference together) waits until there is more than one co-op and a decision to link them. Bosa™ can still offer inference as a single node, under §7.3.

---

## 13. What This Deliberately Is Not

- **Not a data broker** — no sale, no licensing, no intermediation (§7).
- **Not a personal data store** — it holds activity-related data only, not members' whole profiles (§6.1).
- **Not a federation node** — single node, by decision (§12).
- **Not a platform** — no ad model, no engagement metrics, no growth-by-capture; growth by replication only.
- **Not a blockchain** — trusted infrastructure the co-op controls; no consensus mechanism.
- **Not a token-weighted DAO** — one member, one vote (ICA Principle 2).
- **Not trustless execution** — a human acts after every recorded decision.
- **Not a surveillance system** — no contact harvesting, no anagrafe extraction, no logging on the boards.

---

## 14. Open Questions

**For the notaio / cooperative-law consultant:**
- Mixed cooperativa di comunità carrying energy, data custody and AI service in one vehicle; ring-fencing approach (§3.1).
- Statuto drafting: oggetto sociale (§3.2), entrenched no-share/no-sale clause and its amendment threshold (§7.1–7.2), federation clause (§12), electronic assembly voting (§3.4).
- DGA: confirm internal-only use falls outside the intermediation regime (§3.3).
- Dote fund: structure, separation from the co-op, funding from donors or the island-tier Srl (§4.3).
- Apprentice category vs full membership for the youth cohort, given the one-third cap (§4.3).
- Treatment of a dote-funded quota on exit (§4.5).
- Use the Bologna model data-co-op statuto (*EU Data Cooperatives*, appendix) as the starting draft.

**For the Comune:**
- DPO view on an institutional letter to residents, with no data leaving the town hall (§4.4).
- Convenzione scope; board-panel authorisations in the centro storico (§10, §11.2).

**Technical, for the pilot:**
- Per-member collection isolation in Open-WebUI at retrieval time (§6.4).
- GPU queue behaviour with hundreds of members (§5).
- Member-side collector hardware and outbound push for inverter data (§6.2).
- Admin visibility of member chats: what the published policy says (§6.4).
- Aggregation threshold value (§6.4).

**Organisational:**
- Annual fee level; nominal quota amount (≥ €25).
- Data steward and board-rounds roles: who, and how rotated.
- Whether Trade AI participation is in pilot scope at all.

---

## 15. Effect on Other Documents

- **`cer-community-ai.md`** — partly superseded for Bosa™: its producer/consumer tier split and public-layer access are replaced by single-membership access (§4.2); its several-producer-Nodes-federating model is replaced by the single-node rule (§12); sensor data goes to per-member collections, aggregates only to shared (§6.4). Its scope restriction (community-owned installations only) still stands.
- **`spec-community-ai.md` §7, `network-library-public-mirror-overview.md`, `hardware-relaybox.md`** — out of scope for Bosa™; kept as later options.
- **`archiver-protocol.md`** — applies to the board only (§5); members use simple login.
- **`sales-coop-ai.md`** — unchanged; the co-op is the HP counterparty (§9).
- **`DECISIONS.md`** — new entries added for this draft.

---

*Draft v2 — supersedes v1 (2026-09-14). Reframes Coop AI from a board's AI-queryable governance record into the AI and data layer of a single-node cooperativa di comunità, piloted as Bosa™. Adds: single-node rule and deferred federation behind an assembly-vote clause; Comune di Bosa residency as the catchment; the data co-op layer with scoped, consent-based intake and per-member partitioning; the entrenched no-share/no-sale rule covering derived artefacts; CER as first partner with data arriving through member consent; membership classes and the 18–23 youth dote; lawful outreach (workshops, referral, platform-side ads); Bosa™ boards as SUNDIAL's second life; Headscale in place of Tailscale; GitJournal in place of Obsidian. [date: 2026-10-19]*
