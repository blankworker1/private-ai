# Trade AI — Protocol

**Version:** 0.4 (draft)
**Status:** protocol sketch; nothing built or tested
**Depends on:** [model.md](model.md) v0.4. Symbols not defined here are defined there.
**Companion:** [spec-trade-ai.md](spec-trade-ai.md) (the stack on a node)

Formerly `TRADE.md` in the ai-btc-x repo.

## 1. Purpose

model.md fixes the band within which a price for inference can form. This note describes the simplest peer-to-peer market that lets a price form inside it: sellers quote automatically from a formula, buyers find them on an open board, and payment is made directly in sats.

It is a protocol note, not a product. Nothing here should be built before the inputs in model.md §7 have been measured.

## 2. Principles

- **No pool.** Inference cannot be stored, so there is nothing to pool. Each seller quotes for their own machine.
- **No custody.** Buyer pays seller directly. No third party ever holds funds.
- **No operator.** Listings are signed by sellers and published openly. The board is one viewer of them and can be replaced.
- **Quotes are computed, not set.** A seller chooses one number, the margin. Everything else comes from the chain and from the machine's state.
- **Sats only.** No fiat price enters at any point.
- **Auditable, not proven.** A seller's claims can be checked by sampling. They cannot be proven per request.
- **Prices, not telemetry.** A node publishes its ask, its grade and whether it is available. It never publishes load, energy state, battery charge or usage. The node, the formula and the market aggregate data; they do not expose it.
- **Automated, not AI-run.** Quoting, paying, checking and recording are done by deterministic code. AI agents create demand; they do not hold the wallet.

## 3. Nodes and roles

### 3.1 The node

The market is made of nodes of one kind. A node is a site that already has:

- solar PV, with or without a battery;
- a bitcoin miner whose power can be curtailed;
- a small AI machine serving at least one grade;
- the control software that steers the miner against the sun, and so already reads PV power, battery charge and miner draw.

This is deliberate self-selection. Every node has a miner, so every node has a real floor. Every node has the telemetry, so every node can compute its own quote without new hardware. Every node is both a seller and a buyer.

### 3.2 Roles

| Role | Does |
|---|---|
| Seller | Publishes a signed listing; serves jobs; issues receipts |
| Buyer | Reads listings; decides whether to buy, self-produce or defer; pays; spot-checks |
| Auditor | Holds a grade and runs spot-checks for others, for a fee in sats |
| Relay | Carries signed listings, receipts and check reports; holds no funds and sets no prices |
| Board | A web app that displays listings from relays; a viewer only |

A node plays the first two roles and may play the third. Each node is identified by a persistent key.

## 4. Grade

A grade is what makes two sellers' tokens comparable. It is the hash of the fields that model.md §7 fixes for inference:

```
grade_id = SHA-256( weights_hash ‖ quantisation ‖ runtime_version ‖ sampling_settings )
```

Every listing states exactly one grade ID per offer. Prices are comparable only between listings with the same grade ID. Hardware is not part of the grade: the same grade on different hardware is the same product at a different cost.

## 5. The quote

### 5.1 Floor

The seller's floor is the marginal price from model.md §6.8, multiplied by the energy state factor `k`:

```
floor = k × P_m
```

### 5.2 Energy state factor

`k` answers one question: if this joule goes to the GPU, how much hashing is lost, now or later?

| State | What the joule would otherwise do | `k` |
|---|---|---|
| Clipped sun: battery full, miner at maximum, PV held back | Nothing; it is wasted | 0 |
| Sun, miner absorbing | Hash now | 1 |
| Sun, battery charging | Charge the battery, displacing hashing later | 1 |
| Battery, above reserve | Stay stored, so the next day's sun hashes instead of recharging | 1 / (η × h) |
| Battery, at or below reserve | Keep the household running | Off-market |
| Grid import | Costs fiat, outside the model | Off-market |

**The battery case** has two parts:

- **`1 / η` is physical.** A joule taken from the battery needs more than a joule of sun to replace. `η` is the battery's round-trip efficiency.
- **`1 / h` is a scarcity rule**, and a design choice. `h` is headroom, the share of usable charge left above the household's reserve:

```
h = (SoC − SoC_reserve) / (SoC_full − SoC_reserve)
```

As the battery drains, the price climbs; at the reserve the node stops selling. It has the same shape as the congestion rule in §5.3, applied to energy instead of GPU time.

**Inputs.** `k` is computed by the node's control software from PV power, battery state of charge, miner draw and whether PV is being held back. The state and `k` stay on the node and are never published (§6).

**Consequences**

- Nodes differ in weather and longitude, so `k` differs between them at the same moment. Inference moves to where the sun is.
- At night every nearby node is expensive, so deferrable work waits for daylight.
- The ready state draws from the battery overnight. Availability windows follow the sun by default; overnight availability is a choice the node makes knowingly.

**Simplifications**

- The next day is assumed to be a hashing day. If its sun would be clipped anyway, a battery joule tonight is nearly free. Handling this needs a forecast.
- Battery wear is left out. It is a capital cost, which model.md excludes.

`k` extends model.md, which states the first two states in words only.

### 5.3 Congestion rule

```
ask = max( ask_min ,  floor × (1 + m) / (1 − ρ) )
```

| Symbol | Meaning | Set by |
|---|---|---|
| `m` | Margin | Seller |
| `ρ` | Load: share of the last 10 minutes spent working, counting queued jobs | Measured |
| `ask_min` | Lowest price the seller will accept, for when `k = 0` | Seller |
| `SoC_reserve` | Battery charge kept back for the household | Seller |
| `ρ_max` | Load above which no new jobs are accepted (default 0.9) | Seller |

As load approaches full, the ask climbs steeply. Remaining capacity, `1 − ρ`, plays the part that the reserve plays in a liquidity pool: the price rises as it is drained.

The rule is stateless and runs on the node. Its inputs are not published (§6).

**What is published.** The computed ask reveals something about load and energy state to anyone who knows the formula. To limit that, the published ask is:

- rounded up to the next step on a fixed ladder, each step about a quarter above the last;
- updated on a fixed schedule, every 15 minutes, never at the moment something changes;
- replaced by a plain "unavailable" when the node is off-market, with no reason given. A busy household, a battery at reserve and a machine switched off all look the same.

### 5.4 Example

Using the assumed figures in model.md §10 (`P_m` ≈ 94 sats/MTok), with `m = 0.2`.

By load, in daylight with the miner absorbing (`k = 1`):

| Load `ρ` | Ask (sats/MTok) |
|---|---|
| 0.00 | 113 |
| 0.50 | 226 |
| 0.75 | 451 |
| 0.90 | 1,128 |

By energy state, at no load, with `η = 0.9`:

| Situation | `k` | Ask (sats/MTok) |
|---|---|---|
| Midday, clipped | 0 | `ask_min` |
| Daytime, miner absorbing | 1 | 113 |
| Evening, battery full (`h = 1`) | 1.1 | 125 |
| Night, half the headroom used (`h = 0.5`) | 2.2 | 251 |
| Before dawn (`h = 0.2`) | 5.6 | 627 |
| At reserve | — | Off-market |

The two effects multiply. A buyer whose own cost of self-producing is 685 sats/MTok stops buying somewhere past 80% load in daylight, and well before dawn at any load.

### 5.5 Validity

A quote holds for one payment increment (§7). The next increment is priced at the ask then current.

## 6. Listing

A listing is a signed, replaceable message published by the seller and refreshed on a fixed 15-minute schedule.

| Field | Content |
|---|---|
| Seller key | Persistent public key |
| Grade ID | As §4 |
| Status | Available or unavailable |
| Ask | Published price, sats/MTok, rounded as in §5.3 |
| Limit | Maximum output per job |
| Increment | Size of one payment increment, in sats |
| Endpoint | Where to send jobs |
| Timestamp and expiry | A listing past its expiry is ignored |

**What a listing never carries:** load, utilisation, energy state, battery charge, PV output, miner draw, machine power figures, the seller's margin or minimum, or availability windows. These are the household's energy and usage data. They stay on the node.

An earlier draft published the inputs so that anyone could recompute the ask. That was dropped. The inputs are self-reported and cannot be verified, so publishing them bought little trust and gave away when a house is busy, when it is empty and how full its battery is.

Listings follow a fixed, machine-readable schema, so that software can read and act on them without a person. The schema and the relay network are not fixed here. Nostr is the working assumption.

## 7. Settlement

Payment is streamed over Lightning in small prepaid increments.

1. The buyer pays one increment to the seller.
2. The seller serves tokens, drawing the increment down at the quoted ask.
3. When it is nearly spent, the buyer pays the next, at the ask then current, or stops.
4. At the end, the seller issues a signed receipt: grade ID, tokens served, sats paid, epoch.

At no point is either side exposed for more than one increment. This replaces escrow.

The endpoint follows the existing pay-per-request pattern: request, invoice, payment, service. Using that pattern lets current agent and wallet tooling work with a node unchanged.

**Increment size.** At the example prices a typical answer costs a fraction of a sat, so an increment cannot be one request. It has to be a round sum, in the order of 10 to 100 sats, covering many requests. Any unspent part stays as credit with that seller until it expires.

## 8. Proof of grade

A seller could serve a smaller, cheaper model than the one listed. There is no practical cryptographic proof against this on consumer hardware. The protocol makes substitution detectable by sampling instead.

### 8.1 Spot-check

At temperature zero, two honest machines serving the same grade give near-identical output.

1. On a small share of requests, the buyer asks for token probabilities for the first `N` output tokens.
2. The buyer sends the same prompt to a second seller listing the same grade ID.
3. The two responses are compared on token agreement and on the difference in probabilities.
4. Agreement within tolerance passes. Outside it, the check fails.

Because checks are ordinary requests, a seller cannot tell them apart from normal jobs.

The comparison is arithmetic and is run by a script, not by an AI. A model acting as checker would itself need checking.

The second opinion can come from three places:

| Source | When |
|---|---|
| The buyer's own machine | The buyer holds the same grade. No second seller is needed |
| A second seller | Another node lists the same grade ID |
| An auditor | A node that holds the grade and checks for a fee |

| Parameter | Value |
|---|---|
| Sampling rate | _TBD_ |
| `N` | _TBD_ |
| Tolerance | _TBD_, calibrated from honest machines on different hardware |

Exact match is not required, because different GPUs and drivers do not agree bit for bit.

### 8.2 Reputation

- Receipts are signed by the seller and given to the buyer. They are not published.
- Once per difficulty epoch a node may publish totals only: jobs served and tokens served, each as a band, not an exact figure.
- A failed check is published as a signed report by the buyer, with the prompt hash and both responses' probabilities, so that others can repeat it. Publishing it is the buyer's choice.
- The board shows, per seller key: age of key, banded totals, checks passed and failed.

This catches substitution statistically over many jobs. It does not protect any single request. The increment size is the most a buyer can lose before noticing.

## 9. Automation and agents

No trade needs a person's sign-off. A person signs off the policy; software does the rest.

| Layer | Who | Does |
|---|---|---|
| Policy | Human | Sets budgets, ceilings, allowed grades and the battery reserve |
| Mechanism | Deterministic code | Quotes, pays, checks, records |
| Demand | AI agent, or a person | Decides what work is needed and asks for it |

The wallet belongs to the mechanism layer. An agent requests work; it does not pay for it directly. The reason is specific: a buying agent reads the seller's output, and that output may contain text written to manipulate it. Rule-based code is not open to that.

**Safeguards that replace sign-off**

- Caps on spending per increment, per hour and per day.
- A separate spending wallet holding only a small balance.
- A ceiling price and an allowed list per grade.
- A stop switch.
- A complete log of quotes seen, payments made and checks run.

**Risks particular to agents**

- A loop that spends to the cap.
- Many agents acting alike, all buying or all deferring at once.

## 10. The buyer's decision

For each job, the buyer has three options, all from model.md:

| Option | Take it when |
|---|---|
| Buy | The best ask for the grade is below the buyer's own `P(u_b)`, or the buyer has no GPU |
| Self-produce | The buyer's own marginal price is below the best ask |
| Defer | The job can wait and the expected storage ratio `G` favours it |

A buyer may automate this with a ceiling price per grade.

## 11. The board

A web app in the manner of a peer-to-peer listings site.

**It shows**

- Listings by grade, sortable by ask and reputation.
- For each listing: status, the ask and the seller's record.
- For each grade: the current band, from the lowest ask to a reference `P(u_b)`.
- A history of traded prices per grade, as an average across nodes for each epoch, built from totals that nodes choose to contribute. This is series 9 in model.md. No single node's trades are shown.

**It does not**

- Hold funds, match orders, set prices or take a fee from trades.
- Host the listings. It reads them from relays, and anyone can run another copy.

## 12. Open questions

- **Quotes cannot be checked from outside.** Inputs stay on the node by design, so a buyer cannot tell whether an ask follows the formula. Competition between sellers is the only check on this.
- **An ask still reveals something.** Rounding and scheduled updates reduce what can be inferred about a household from its price over time. They do not remove it.
- **Reputation from private receipts is weaker.** Banded totals are self-reported. Whether buyers should co-sign them is undecided.
- **Detecting clipped sun.** `k = 0` depends on knowing when PV is being held back. Whether the inverter reports this reliably needs confirming on real hardware.
- **Nearby nodes share the same sky.** Nodes in one region have the same night and much of the same weather, so `k` may differ little between them. The energy reason to trade grows with geographic spread.
- **Prompts are visible to the seller.** A buyer's prompt is read in the clear by whoever serves it, and a spot-check shows it to a second seller. This is unsuitable for private material.
- **Reputation can be manufactured.** New keys are free, and a seller can buy from themselves. Age and volume raise the cost of this without removing it.
- **Spot-checks need a second opinion.** A grade held by one node only cannot be cross-checked. A public set of reference prompts and outputs per grade would cover this case.
- **Auditors need auditing.** An auditor's reports carry weight only through its own record. How that record is built is undecided.
- **Tolerance is unmeasured.** How far honest machines differ is not yet known, so the tolerance cannot yet be set.
- **Small payments.** Sub-sat amounts, channel liquidity and fees on small increments all need testing.
- **Credit left with a seller.** Unspent increments are a small balance held by the seller. Whether that needs a refund path is undecided.
- **Legal status.** Selling a service for bitcoin and running a listings board may carry obligations that vary by country. This has not been looked at.

## 13. Community offers

A future form of the market, and probably its usual one. A community of nodes publishes one combined offer per grade through a shared gateway, in place of a listing per household. It is the inference counterpart of a mining pool.

### 13.1 How it works

- Each member node computes its ask as in §5 and tells only the community gateway.
- The gateway publishes one offer per grade. Its price is the ask of the next node in line.
- Jobs go to the cheapest available node first. This is the merit order used in electricity markets: the sunniest, least busy node serves.
- The gateway matches buyer to node and passes on that node's own invoice. Payment goes direct. The gateway holds no funds.
- Member nodes fetch jobs from the gateway over an outbound connection. The gateway never opens a connection into a household.

### 13.2 What it gains

- **Privacy.** Buyers see only the aggregate. No household's price, status or pattern is visible outside the community. This serves "prices, not telemetry" better than rounding each node's ask.
- **No household exposed.** Only the gateway faces the internet.
- **Availability.** One house is often busy or dark. Many rarely are at once.
- **Cost.** Pooled demand keeps machines busier, which moves the community down the parity curve.
- **Trust.** Reputation attaches to a known community. Members holding the same grade can check each other (§8).

### 13.3 What it does not do

It adds capacity and reliability, not capability. Many small machines cannot practically combine to run one large model over ordinary connections. The offer is more of a grade, always on.

### 13.4 Who buys

A community with members who have no hardware already has its buyers. In a renewable energy community, producer-members run nodes and consumer-members do not. Consumer-members are known to the community, which widens what they can reasonably send it.

### 13.5 What it needs from governance

A coordinator returns, and it sees every prompt. Who runs the gateway, who may join, the community's margin and how disputes are settled are decisions for the community's existing governance, recorded as policy and applied by a person. Nothing here needs a mechanism of its own.

The gateway should be thin and replaceable, and a community should be free to use more than one. Mining pools show where a single indispensable coordinator leads.

### 13.6 Individual listings

Listings by a single node (§6) remain for a node that belongs to no community. Individuals are the early adopters; once communities form, the lone node is the exception.

### 13.7 Fit with the rest of private-ai

Three statements elsewhere in the repo need reconciling before this is built. See `spec-trade-ai.md` §13.

## 14. Next steps

1. Complete the measurements in model.md §13.
2. Run two machines on one grade and measure how far their outputs differ, to set the tolerance in §8.1.
3. Confirm that the control software can report all six energy states, including clipped sun.
4. Fix the listing schema.
5. Test streamed payment between two machines at realistic increment sizes.

## Changes from 0.3

- Added community offers (§13): one combined offer per grade through a shared gateway, merit-order dispatch, direct payment, outbound-only job fetching.

## Changes from 0.2

- New principle: prices, not telemetry.
- Listings no longer carry inputs, energy state, limits or availability windows; only status, a rounded ask, grade and what a buyer needs to connect (§6).
- The published ask is rounded to a ladder and updated on a fixed schedule; "unavailable" gives no reason (§5.3).
- Receipts are private. Reputation and price history use banded, per-epoch totals (§8.2, §11).

## Changes from 0.1

- Defined the node (§3.1) and added the auditor role.
- Replaced the three-case energy factor with six states and a headroom rule for the battery (§5.2).
- Added automation and agents (§9): three layers, safeguards, and why the wallet sits outside the agent.
- Proof of grade: checks are scripted; the buyer's own machine or an auditor can give the second opinion.
- Listings carry energy state and follow a fixed schema; the endpoint follows the pay-per-request pattern.
