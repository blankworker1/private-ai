# Pricing Inference in Sats

## A joule-denominated parity model for solar sites that both mine and infer

**Working paper, draft v0.2** — 7 October 2026
**Author:** _to be added_
**Status:** theoretical. No measured data. Worked figures are illustrative.
**Companions:** [model.md](model.md), [protocol.md](protocol.md), [spec-trade-ai.md](spec-trade-ai.md)

---

## Abstract

A site with solar generation, a curtailable bitcoin miner and a small AI machine has two flexible loads on one energy source. We show that the miner gives every surplus joule an opportunity cost in sats, and that this is enough to price inference in sats with no fiat price in the chain. The parity price of a token is the hashing yield in sats per joule multiplied by the energy per token. Because an inference machine draws power while waiting and a miner does not, the parity price is a curve over utilisation and not a single number. From it we derive a marginal price, a price of availability, a break-even utilisation, a trading band between two sites, an energy state factor for sun, battery and night, and a storage ratio that describes what a joule saved as sats buys later. Each component has clear precedent. The contribution is the combination, and its application at household scale. We set out a measurement protocol aligned with recent proposals for reporting energy per token, and outline a peer-to-peer market in which quotes are computed from the model.

---

## 1. Introduction

Bitcoin mining and AI inference are usually presented as rivals for the same megawatt. Since 2024, listed miners have been comparing revenue per megawatt-hour from hashing with revenue from hosting AI, and many have moved capacity from one to the other [7]. At that scale the choice is forced by power contracts and capital.

At the scale of a household or a small cooperative with its own solar, the two loads do different jobs. A miner takes whatever is spare, at any moment, with no customer. An inference machine earns more per joule, but only when someone is asking. This paper treats them as complements on one site and asks a narrow question: **what is the price of a token, in sats, at which a joule is indifferent between the two uses?**

The answer needs no dollar price, no exchange rate and no oracle. It needs two measured efficiencies and two numbers read from the Bitcoin chain.

**Contributions.** We claim a synthesis, not a discovery.

1. A parity price for inference in sats, obtained by replacing the electricity tariff in a standard marginal-cost expression with the hashing yield (§4.1–4.3).
2. Explicit treatment of the inference machine's ready state, giving a parity curve, a price of availability and a break-even utilisation with a simple closed form (§4.4–4.6).
3. A trading band between two sites, bounded below by the seller's physics and above by the buyer's cost of self-production (§4.7).
4. An energy state factor covering clipped sun, displaced hashing and battery discharge (§4.8).
5. A storage ratio for deferred spending, separating a network factor from an efficiency factor (§4.9).
6. A measurement and reporting protocol (§7).

We found no prior statement of items 1, 3, 4 or 5 in this form. A limited search cannot establish absence, and the claim of novelty is provisional.

---

## 2. Background and related work

**Hashprice and revenue per unit of energy.** Miners measure expected revenue per unit of hashrate per day, a quantity known as hashprice [8]. Dividing by machine efficiency gives revenue per unit of energy. Since late 2024 the industry has used revenue per megawatt-hour to compare mining with AI and high-performance computing on equal terms. One early treatment put a current-generation miner at about $126/MWh against a wide and partly unverified range of $200–500/MWh for AI-focused facilities [7]. Our model makes the same comparison, at the level of a watt instead of a site, and inverts it into a price per token.

**Mining as a floor under energy.** It is well established that mining acts as a buyer of last resort for electricity: any producer, anywhere, can sell energy to the network at the prevailing hashprice. Walton describes bitcoin as determining "the minimum value for a unit of electricity" [6]. A related line runs the other way and values bitcoin by its energy input, as in Hayes's cost-of-production model [4] and Edwards's Energy Value [5]. We use only the first idea: the miner fixes what a joule is worth in sats.

**Energy per token.** A 2026 position paper argues that inference should be evaluated as energy-to-token production, proposes joules per token at fixed quality and service targets as the reporting standard, and gives marginal cost as approximately electricity price × PUE × energy per token, plus a residual [9]. TokenPowerBench provides a measurement framework at node level and reports, among other results, that energy per token rises about 7.3× from a 1B to a 70B model, falls 25–40% with an optimised inference engine, and falls about 30% moving from FP16 to FP8 [10]. Our parity price has the same structure as the marginal-cost expression in [9], with two changes: the tariff is replaced by the hashing yield, and the utilisation factor is replaced by explicit ready-state power.

**Inference markets settled in sats.** Routstr is a live peer-to-peer market for inference in which node operators publish their presence on Nostr and are paid in Cashu ecash backed by bitcoin [12]. Prices there are set by operators and converted from dollars, most nodes resell an upstream provider, and the published description includes no check that a node serves the model it advertises. HTTP 402 payment patterns, including L402 over Lightning and the more recent addition of Lightning to x402, provide pay-per-request rails [13]. These are the nearest neighbours to the market sketched in §8. None uses energy to set a price.

**Verifying what was served.** TOPLOC commits to the top-k activations of a model's last hidden state and lets a verifier detect a changed model, prompt or numerical precision, with a proof of about 258 bytes per 32 new tokens and validation much faster than generation [11].

**"Past compute buys future compute."** Proof-of-work as a way of attaching physical cost to digital action goes back to Hashcash [2] and is developed at length by Lowery [3]. A recent version of the argument, known to us only through secondary summaries, holds that bitcoin is tokenised past computation that can be accumulated to buy future inference. Read literally this is a category error: bitcoin is not a claim on compute. Section 4.9 gives a form of the claim that does hold.

---

## 3. Setting and assumptions

**The site.** Solar PV, optionally a battery, a miner whose power can be curtailed, and an AI machine serving a fixed open-weights model.

**Assumptions.**

- Energy is surplus solar: no marginal cost, and lost if unused. The miner hashes whatever nothing else is using.
- Sats are the only unit of payment. No fiat price or outside price level is used.
- Network difficulty and block reward are taken as given.
- Capital cost is excluded. The model prices energy only (§9).

**Machine states.**

| Machine | State | Draws | Produces |
|---|---|---|---|
| Miner | Off | ~0 | Nothing |
| Miner | Hashing | `E_h` per terahash | Sats |
| GPU | Off | ~0 | Nothing |
| GPU | Ready | `P_ready` | Availability only |
| GPU | Working | `P_work` | Tokens |

The miner consumes only while it produces. The GPU can consume without producing. Most of what follows comes from this asymmetry.

**Stock and flow.** Both machines have capacity that is lost when idle. The miner converts joules into a durable, fungible asset, needing no counterparty. The GPU converts joules into inference only on demand, and the output is consumed on delivery or kept as a specific work product. The model therefore prices a flow in a stock, which is why inference is quoted in bitcoin and not the reverse.

---

## 4. The model

Notation is collected in Appendix A. The joule is the base unit; tokens are output tokens.

### 4.1 Hashing yield

Expected work per block, in terahashes, at difficulty `D`:

```
W = D × 2³² / 10¹²
```

With mean block reward `R` (subsidy plus fees, in sats) and miner efficiency `E_h` (J/TH), the hashing yield in sats per joule is:

```
S_J = R / (W × E_h)                                           (1)
```

`R` and `D` are averaged over one difficulty epoch of 2,016 blocks. `S_J` is hashprice divided by efficiency, restated per joule. It uses on-chain data only.

### 4.2 Energy per token

Ready-state energy is spread across the tokens actually produced. At utilisation `u`, the share of ready time spent working, and generation rate `r` (tokens per second):

```
E_t(u) = 1000 × (P_work × u + P_ready × (1 − u)) / (r × u)      [J/kTok]   (2)
```

### 4.3 Parity curve

```
P(u) = S_J × E_t(u) × 1000                                     [sats/MTok] (3)
```

`P(u)` is the price at which a joule earns the same hashing or inferring. `P(1)` is its minimum.

**Relation to prior work.** Equation (3) is the marginal-cost form of [9] with the tariff `p_e` replaced by `S_J`, with PUE absorbed by measuring at the wall, and with the residual omitted.

### 4.4 Marginal price

For a machine already in the ready state, an extra job costs only the step from ready to working:

```
P_m = S_J × (P_work − P_ready) / r × 10⁶                        [sats/MTok] (4)
```

### 4.5 Price of availability

Ready-state power is hashing foregone:

```
A = S_J × P_ready × 3600                                        [sats/hour] (5)
```

**Decomposition.** Substituting (4) and (5) into (3):

```
P(u) = P_m + A × 10⁶ / (3600 × r × u)                                      (6)
```

The parity price is the marginal price plus the cost of availability spread over the tokens served. As `u → 0` the second term grows without limit.

### 4.6 Break-even utilisation

For an offered price `p > P(1)`, setting `P(u*) = p` in (6) gives the minimum utilisation at which inference matches hashing:

```
u* = (P(1) − P_m) / (p − P_m)                                              (7)
```

Above `u*` the machine should stay ready. Below it, the machine should switch off and accept a start-up delay, or offer availability only in set windows.

### 4.7 Trading band

Consider a seller in the ready state and a buyer who could self-produce at utilisation `u_b`. Trade leaves both better off at any price in:

```
P_m (seller)  <  price  <  P(u_b) (buyer)                                  (8)
```

The model fixes the band. It does not fix the price inside it; that price is the market-defined rate between sats and inference. The band is widest between a seller with spare ready capacity and a buyer with low utilisation, so the shape of `P(u)` is itself the reason to trade.

### 4.8 Energy state factor

Equation (1) assumes the joule would otherwise be hashed. That is one of several cases. Let `k` scale the seller's floor, `floor = k × P_m`:

| State | What the joule would otherwise do | `k` |
|---|---|---|
| Clipped sun: battery full, miner at maximum | Nothing; it is wasted | 0 |
| Sun, miner absorbing | Hash now | 1 |
| Sun, battery charging | Charge, displacing hashing later | 1 |
| Battery, above reserve | Stay stored, so the next day's sun hashes instead of recharging | 1 / (η × h) |
| Battery, at reserve; or grid import | — | Off-market |

`η` is the battery's round-trip efficiency, which is physical. `h` is headroom, the share of usable charge above the household's reserve; `1/h` is a scarcity rule and a design choice.

### 4.9 Time: the storage ratio

Every quantity above is a snapshot at one epoch. `E_t` is the physical floor; `P` is its translation into sats at that epoch. A halving halves `R`, and so `P`, at once, and leaves `E_t` untouched.

A joule of surplus at epoch `t₀` can be used directly, giving `1000 / E_t(u, t₀)` tokens, or mined into `S_J(t₀)` sats and spent on inference at parity at `t₁`. The ratio of the second to the first, at equal utilisation, is:

```
G(t₀, t₁) = (S_J(t₀) / S_J(t₁)) × (E_t(u, t₀) / E_t(u, t₁))               (9)
```

| Factor | Driven by | Usual direction |
|---|---|---|
| Network, `S_J(t₀) / S_J(t₁)` | Difficulty and the subsidy schedule | Above 1; doubles at a halving |
| Efficiency, `E_t(t₀) / E_t(t₁)` | Hardware, runtime and model progress | Above 1 |

When `G > 1`, a joule stored as sats buys more inference later than it could have produced on the day. Inference cannot be stored; sats can. This is the defensible form of "past compute buys future compute": bitcoin is not a claim on compute, but it is a joule-denominated store that converts at parity.

`G` is realised only against a counterparty whose alternative is hashing at the later yield. It is not guaranteed: if difficulty falls, the network factor is below 1.

---

## 5. Properties

1. **No fiat term.** Equations (1)–(9) contain no fiat price. `S_J` is computed from chain data and one measured efficiency.
2. **Halving invariance.** `E_t` does not depend on `R`. A halving changes the sats expression of the floor and not the floor.
3. **Marginal below average.** Since `P_ready > 0`, `P_m < P(1) ≤ P(u)` for all `u`. A ready machine can always profitably accept a job priced between `P_m` and `P(1)`.
4. **Sign asymmetry.** The miner's joule account is never negative, because it consumes only while producing. The GPU's position against the miner is negative whenever `u < u*`.
5. **Doubly falling price.** Under rising difficulty and improving inference efficiency, both factors of `P = S_J × E_t` fall. The sats price of a fixed grade of inference then falls from both sides.
6. **A band, not a point.** By (8), a non-empty band exists whenever `P_m` of the seller is below `P(u_b)` of the buyer. Identical machines at different utilisations satisfy this.

---

## 6. Illustration

All inference inputs are assumptions. They are of the same order as an unmeasured load estimate for a workstation with a 16 GB consumer GPU, and are not results.

| Input | Value | Source |
|---|---|---|
| Network hashrate | ~1.16 ZH/s | [14] |
| `R` | ~3.15 × 10⁸ sats | Subsidy plus assumed fees |
| `E_h` | 23 J/TH | Nameplate |
| `P_work`, `P_ready` | 250 W, 60 W | Assumed |
| `r` | 40 tokens/s | Assumed |

| Output | Value |
|---|---|
| `S_J` | 1.97 × 10⁻⁵ sats/J (≈ 71 sats/kWh) |
| `P(1)` | 123 sats/MTok |
| `P_m` | 94 sats/MTok |
| `A` | 4.3 sats/hour |

| `u` | `E_t(u)` (J/kTok) | `P(u)` (sats/MTok) |
|---|---|---|
| 1.00 | 6,250 | 123 |
| 0.50 | 7,750 | 153 |
| 0.25 | 10,750 | 212 |
| 0.10 | 19,750 | 389 |
| 0.05 | 34,750 | 685 |

- **Break-even.** At `p = 246` sats/MTok, twice `P(1)`: `u* = (123 − 94) / (246 − 94) ≈ 0.19`.
- **Band.** A ready seller and a buyer who would self-produce at 5% utilisation can trade anywhere between 94 and 685 sats/MTok.
- **Storage.** Across one halving with nothing else changed, `G = 2`. If joules per token also halve, `G = 4`. If difficulty instead falls by a quarter, `G = 1.5`.

At low utilisation the result is dominated by `P_ready`; at high utilisation by `r` and `P_work`. `P_ready` is the easiest input to measure and the most likely to be wrong here.

---

## 7. Measurement and reporting

Two results are comparable only if they share reference conditions. We adopt the reporting recommendations of [9] and the node-level approach of [10], adapted to a single machine.

**Fix and disclose**

- Model, with weights file hash and quantisation. Published work shows quantisation alone shifting energy per token by about 30% [10].
- Runtime and version. Engine choice shifts it by 25–40% [10].
- Prompt set, input length and output length. Longer inputs raise energy per token substantially [10].
- Temperature, concurrency and batch size. The reference condition here is single stream.
- Hardware.
- Energy boundary: wall power for the whole machine. This plays the part that PUE plays at facility scale.

**Measure**

1. From off: start-up time and energy.
2. Model loaded, no job, for 10 minutes: `P_ready`.
3. Prompt set in a loop for at least 30 minutes: `P_work` and `r`.
4. Miner at a stated power setting for at least 24 hours: `E_h` from energy and pool-accepted hashrate.

**Report**

- Joules per token at the stated conditions, with `P_ready`, `P_work` and `r` given separately so that `E_t(u)` can be reconstructed for any `u`.
- `S_J`, with the epoch used.
- Any figure not taken from a meter, labelled as nameplate.

The identifier of a set of reference conditions, the grade, is the hash of the model, quantisation, runtime and sampling settings. A token is always quoted per grade. No quality adjustment between grades is attempted.

---

## 8. Application: a market between nodes

The model supports a peer-to-peer market with no pool, no custody and no operator. The design is set out in `protocol.md`; this section records how it relates to existing practice.

**Quotes are computed.** A seller chooses a margin `m`. The ask follows from the model and the machine's state:

```
ask = max( ask_min ,  k × P_m × (1 + m) / (1 − ρ) )                       (10)
```

`ρ` is recent load. The `1/(1 − ρ)` term is the familiar congestion form from queueing: the price rises as remaining capacity is used up, as a liquidity pool's price rises when one asset is drained. Existing sats-settled markets set prices by converting from dollars [12]. Here the price is derived from the chain, the machine and the sky.

**Discovery.** Signed listings published to open relays, with a web board as one viewer, following the pattern already in use [12]. A listing carries the grade, a rounded ask and whether the node is available, and nothing else. The inputs to (10) are a household's energy and usage data, and stay on the node. The design principle is that the node, the formula and the market aggregate data and do not expose it.

**Settlement.** At the prices in §6 a single answer costs a fraction of a sat, so per-request Lightning payment is impractical. Two workable patterns exist: a prepaid increment of tens of sats drawn down by metering, in the manner of L402 [13]; or ecash tokens attached to requests, as Routstr does [12], which handles small amounts at the cost of trusting a mint.

**Verification.** A seller could serve a cheaper model than the one listed. The published state of the art is activation-based commitment [11], which detects model, prompt and precision changes with a small proof. We recommend it in place of ad hoc output comparison. It requires the seller's inference engine to export the commitment and the verifier to hold the same model.

**Automation.** Quoting, paying and checking are deterministic. A person sets policy: spending caps, ceiling prices, allowed grades, battery reserve. Where AI agents create demand, they do not hold the wallet, since a buying agent reads seller output that may be written to manipulate it.

---

## 9. Limitations

- **Energy is not the whole cost.** Hardware depreciation dominates the cost of inference, and the energy is assumed free. With either included, mining too can run at a loss. `P(u)` is an energy-parity price, not a full cost.
- **Probably not cheaper than a data centre.** Large providers batch many requests on more efficient hardware; batching alone moves energy per token by a factor of two to three [10]. The case for a node is ownership and access, not price.
- **Earnings are small.** At 40 tokens per second a machine produces about 3.5 million tokens a day at most.
- **Demand enters only as `u`.** The model does not predict utilisation.
- **Tokens are graded, not fungible,** and hashes verify themselves where tokens do not.
- **Quotes cannot be checked from outside.** The inputs stay on the node, by design, to keep household energy data private.
- **Neighbouring nodes share a sky.** The energy factor differs little within a region.
- **The floor fades.** As the subsidy shrinks, the miner becomes a weaker alternative, and the floor is set increasingly by capital and the joule's next-best use.
- **Deferral can starve the market.** A unit that rewards waiting may reduce present demand. How much this matters is disputed.
- **Open weights are a dependency** outside the model.
- **No empirical results.** Every inference figure in §6 is assumed.

---

## 10. Further work

1. Measure `P_ready`, `P_work`, `r` and `E_h` on one site and publish them with the epoch.
2. Add capital, as hardware price in sats spread over a working life, giving a second floor beneath the energy one.
3. Replace the assumption that the next day is a hashing day with a forecast, so that `k` reflects expected clipping.
4. Extend the reference conditions to batched and concurrent serving.
5. Run two nodes on one grade to test the band and to calibrate verification.
6. Keep the series from the first measurement, so that `G` can be observed and not only projected.

---

## 11. Conclusion

A miner on surplus solar makes the joule a unit of account in sats. An inference machine on the same site can then be priced against it, by a formula with no fiat term. The price is a curve, because readiness costs energy; its lower bound is physical, its upper bound is the buyer's cost of doing the work themselves, and what lies between is left to a market. Carried through time, the same arithmetic shows how a joule saved as sats becomes more inference later.

None of the parts is new. Joined together, they describe a site where mining and inference are not alternatives: one stores what the sun provides, the other spends it.

---

## Appendix A. Notation

| Symbol | Meaning | Unit |
|---|---|---|
| `D` | Network difficulty | — |
| `R` | Mean block reward, subsidy plus fees | sats/block |
| `W` | Expected work per block | TH |
| `E_h` | Miner efficiency | J/TH |
| `S_J` | Hashing yield | sats/J |
| `P_ready`, `P_work` | Machine power, ready and working | W |
| `r` | Generation rate while working | tokens/s |
| `u` | Utilisation: share of ready time spent working | 0–1 |
| `E_t(u)` | Energy per thousand output tokens | J/kTok |
| `P(u)` | Parity price | sats/MTok |
| `P_m` | Marginal price in the ready state | sats/MTok |
| `A` | Price of availability | sats/hour |
| `p` | An offered price | sats/MTok |
| `u*` | Break-even utilisation | 0–1 |
| `u_b` | A buyer's own utilisation if self-producing | 0–1 |
| `k` | Energy state factor | — |
| `η` | Battery round-trip efficiency | 0–1 |
| `h` | Battery headroom above reserve | 0–1 |
| `m` | Seller's margin | — |
| `ρ` | Recent load | 0–1 |
| `G` | Storage ratio between two epochs | — |

## References

1. S. Nakamoto. *Bitcoin: A Peer-to-Peer Electronic Cash System.* 2008. https://bitcoin.org/bitcoin.pdf
2. A. Back. *Hashcash: A Denial of Service Counter-Measure.* 2002.
3. J. P. Lowery. *Softwar: A Novel Theory on Power Projection and the National Strategic Significance of Bitcoin.* MIT thesis, 2023.
4. A. S. Hayes. *A Cost of Production Model for Bitcoin.* Working paper, 2015.
5. C. Edwards. *Bitcoin Energy-Value Equivalence.* Capriole, 2019. https://capriole.com/bitcoin-energy-value-equivalence
6. P. Walton. *The Joule Paradox: Energy Sets the Value of Bitcoin and Bitcoin Sets the Value of Energy.* Bitcoin Magazine, December 2024. https://bitcoinmagazine.com/technical/the-joule-paradox-energy-sets-the-value-of-bitcoin-and-bitcoin-sets-the-value-of-energy
7. BlocksBridge Consulting. *A New Metric for Bitcoin Miners: Revenue per Megawatt-Hour.* c. November 2024. https://blocksbridge.substack.com/p/bitcoin-mining-dollar-megawatt-hour
8. Luxor Technology. *Bitcoin Hashprice Index.* Hashrate Index. https://data.hashrateindex.com/chart/bitcoin-hashprice-index
9. *Position: LLM Inference Should Be Evaluated as Energy-to-Token Production.* arXiv:2605.11733, 2026. https://arxiv.org/abs/2605.11733 (authors to be confirmed from the paper)
10. C. Niu, W. Zhang, J. Li, Y. Zhao, T. Wang, X. Wang, Y. Chen. *TokenPowerBench: Benchmarking the Power Consumption of LLM Inference.* arXiv:2512.03024. https://arxiv.org/abs/2512.03024
11. J. M. Ong, M. Di Ferrante, A. Pazdera, R. Garner, S. Jaghouar, M. Basra, M. Ryabinin, J. Hagemann. *TOPLOC: A Locality Sensitive Hashing Scheme for Trustless Verifiable Inference.* ICML 2025, PMLR 267. https://arxiv.org/abs/2501.16007
12. Routstr. *Routstr — Decentralized AI powered by Bitcoin and Nostr.* Cashu blog, September 2025. https://blog.cashu.space/routstr/
13. Lightning Labs, *L402* protocol; and *Block joins x402 with Lightning support*, crypto.news, September 2026. https://crypto.news/block-joins-x402-with-lightning-support/
14. CoinWarz. *Bitcoin Hashrate Chart.* Accessed 7 October 2026. https://www.coinwarz.com/bitcoin-hashrate
