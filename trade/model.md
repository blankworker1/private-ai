# Trade AI — Model

**Version:** 0.4 (draft)
**Status:** theoretical model; no measured data yet
**Companions:** [protocol.md](protocol.md) (the market between nodes), [spec-trade-ai.md](spec-trade-ai.md) (the stack on a node)

Formerly `SPEC.md` in the ai-btc-x repo.

## 1. Purpose

To show that AI inference can be priced in bitcoin without passing through a fiat price, by using the joule as the shared denominator.

A miner converts a joule into sats at a rate fixed by the Bitcoin network. A GPU converts a joule into tokens at a rate fixed by its hardware, its model and how busy it is. Dividing one by the other gives an exchange rate between sats and tokens.

The result is a local rate: bounded below by physics and above by the buyer's cost of doing it themselves, discovered in sats with no fiat involved.

## 2. Scope

**In scope**

- A definition of the exchange rate and its units.
- Reference conditions that make two measurements comparable.
- A measurement procedure that one site with one miner, one GPU and one energy meter can carry out.

**Out of scope**

- A trading venue, payment gateway or token. The model describes the band within which a price can form (§6.8), not the mechanism by which parties meet or settle.
- Scaling, routing or reselling third-party compute.
- Verifying that inference was performed correctly (see §11).

**Assumptions**

- Energy is surplus solar: no marginal cost, and lost if not used. The miner hashes whatever surplus nothing else is using.
- Sats are the only unit of payment. The model uses no fiat price and no outside price level.
- Network difficulty is taken as given.

## 3. Stock and flow

The two machines differ in what a joule becomes.

| | Miner | GPU |
|---|---|---|
| Converts joules into | A durable, fungible digital asset | Inference |
| Needs demand to produce | No | Yes, its own or another's |
| Output storable | Perfectly, with no degradation | Consumed on delivery, or kept as a specific work product |
| Output usable as a general store of value | Yes | No |
| Capacity when not running | Lost | Lost |

The model therefore prices a flow in a stock. This is why inference is quoted in bitcoin and not the reverse: the perishable thing is priced in the durable one.

Sunlight, miner capacity and GPU capacity are all perishable. The sat is the only durable thing in the system, and mining is the only path from the perishable to the kept.

Two qualifications:

- What the miner stores is value, not recoverable energy, and quantity, not purchasing power.
- Inference done ahead of demand (embeddings, an indexed library, cached answers) can be kept, but only as a product useful to its owner. This is the deferrable part of the inference load.

## 4. Machine states

| Machine | State | Draws | Produces |
|---|---|---|---|
| Miner | Off | ~0 | Nothing |
| Miner | Hashing | `E_h` per terahash | Sats |
| GPU | Off | ~0 | Nothing |
| GPU | Ready | `P_ready` | Availability only |
| GPU | Working | `P_work` | Tokens |

The miner consumes only while it produces. The GPU can consume without producing. Everything in §6 follows from this asymmetry.

Moving the GPU from off to ready costs a start-up time `t_start` and energy `J_start`.

## 5. Units

| Symbol | Meaning | Unit |
|---|---|---|
| `E_h` | Miner efficiency | J/TH |
| `R` | Mean block reward, subsidy plus fees | sats/block |
| `D` | Network difficulty | dimensionless |
| `S_J` | Hashing yield | sats/J |
| `P_ready` | GPU machine power, model loaded, no job | W |
| `P_work` | GPU machine power while generating | W |
| `r` | Generation rate while working | tokens/s |
| `u` | Utilisation: share of ready time spent working | 0–1 |
| `E_t(u)` | Inference efficiency at utilisation `u` | J/kTok |
| `P(u)` | Parity price at utilisation `u` | sats/MTok |
| `A` | Price of availability | sats/hour |
| `p` | A given price for inference | sats/MTok |
| `u*` | Break-even utilisation at price `p` | 0–1 |
| `P_m` | Marginal price for a seller already in the ready state | sats/MTok |
| `u_b` | A buyer's own utilisation if they self-produce | 0–1 |
| `t` | Time, counted in difficulty epochs | epoch |
| `G` | Storage ratio between two epochs | ratio |

The joule is the base unit. Sats per kWh (`S_J × 3.6 × 10⁶`) is the display unit. Tokens are output tokens.

## 6. The model

### 6.1 Hashing yield

Expected work per block, in terahashes:

```
W = D × 2³² / 10¹²
```

Sats per joule:

```
S_J = R / (W × E_h)
```

`R` and `D` are taken as the mean over one difficulty epoch (2,016 blocks), so the yield updates once per epoch and needs no price oracle.

### 6.2 Inference efficiency

Ready-state energy is spread across the tokens actually produced:

```
E_t(u) = 1000 × (P_work × u + P_ready × (1 − u)) / (r × u)
```

At `u = 1` this is the machine's best case. As `u` approaches zero, it grows without limit.

### 6.3 Parity curve

```
P(u) = S_J × E_t(u) × 1000
```

`P(u)` is the price at which a joule earns the same whether it hashes or infers, at utilisation `u`. It is a curve, not a single number. `P(1)` is its floor.

### 6.4 Price of availability

Ready-state power is hashing foregone:

```
A = S_J × P_ready × 3600
```

This is what it costs, in sats per hour, to keep inference available whether or not anyone asks.

### 6.5 Break-even utilisation

For a given price `p`, the minimum utilisation at which inference matches hashing:

```
u* = P_ready / (p × r / (10⁶ × S_J) − (P_work − P_ready))
```

A break-even exists only if `p > P(1)`.

- **Above `u*`:** stay ready.
- **Below `u*`:** switch off and accept `t_start`, or offer availability only in set windows.

### 6.6 Sign

Hashing yield is never negative and is positive in expectation, though it shrinks with difficulty and halvings. The GPU's position against the miner goes negative whenever `u < u*`, because the ready state consumes joules that would otherwise have become sats.

### 6.7 Time: deferred spending

Every quantity above is a snapshot at one epoch. Written with time, the hashing yield is `S_J(t)` and the inference efficiency is `E_t(u, t)`.

**The invariant.** The floor in joules, `E_t(u, t)`, is the physical quantity. The floor in sats, `P(u, t)`, is its translation at epoch `t`. A halving changes the translation at once and leaves the physical floor untouched.

**Two routes for one joule.** A joule of surplus at epoch `t₀` can be:

- **Used directly:** sent to the GPU, giving `1000 / E_t(u, t₀)` tokens now.
- **Stored:** sent to the miner, giving `S_J(t₀)` sats, then spent on inference at parity at a later epoch `t₁`.

**Storage ratio.** The tokens obtained by the stored route, divided by the tokens from the direct route, at equal utilisation:

```
G(t₀, t₁) = (S_J(t₀) / S_J(t₁)) × (E_t(u, t₀) / E_t(u, t₁))
```

The two factors are independent:

| Factor | Driven by | Usual direction |
|---|---|---|
| Network factor, `S_J(t₀) / S_J(t₁)` | Difficulty and the subsidy schedule | Above 1; doubles at a halving |
| Efficiency factor, `E_t(t₀) / E_t(t₁)` | Hardware, runtime and model progress | Above 1 |

When `G > 1`, deferring pays: the joule stored as sats buys more inference later than it could have produced on the day. Inference cannot be stored; sats can. Mining now and spending later is how inference capacity is carried through time. Bitcoin acts here as a joule-denominated store that converts at parity.

**Conditions**

- **A counterparty is required.** Spending one's own sats on one's own GPU realises nothing. `G` is real only against a seller whose alternative is hashing at the later yield. It is a community-scale effect.
- **No one at parity loses.** The later seller is indifferent. The gain comes from the rest of the network spending more joules per sat, and from technical progress.
- **It is not guaranteed.** If difficulty falls between the two epochs, the network factor is below 1.
- **It holds at the floor.** Actual prices sit above parity.
- **The efficiency factor depends on what is held fixed.** For the same named model on the same hardware it is close to 1. It rises with new hardware or runtime, or with a new reference version linked as in §12.

### 6.8 The trading band

A market needs parties who differ. In this model they can differ in four ways:

| Difference | One party | The other |
|---|---|---|
| Time | Holds sats mined at an earlier epoch | Is selling now |
| Utilisation | Has a GPU ready with little to do | Has demand and no spare capacity |
| Energy | Has surplus at this moment | Does not |
| Hardware | Has only a miner | Has a GPU |

**Lower bound: the seller's marginal price.** A seller already in the ready state has paid for availability. An extra job costs only the step from ready to working:

```
P_m = S_J × (P_work − P_ready) / r × 10⁶
```

`P_m` is below `P(1)`. Two variations:

- A seller who would have to start up for the job faces `P(u)` at the resulting utilisation, plus `J_start`.
- A seller whose surplus exceeds what the miner can absorb is giving up no sats, and the lower bound falls towards zero.

**Upper bound: the buyer's cost of doing it themselves.** A buyer with a GPU of their own would pay `P(u_b)` at their own utilisation. A buyer with no GPU has no upper bound from the model.

**The band.** Trade benefits both sides at any price between the two:

```
P_m (seller)  <  price  <  P(u_b) (buyer)
```

The model fixes the band. It does not fix the price inside it. That price is the market-defined exchange rate between sats and inference.

The band is widest between a seller with spare ready capacity and a buyer with low utilisation. The curve `P(u)` is therefore itself the reason to trade: low-use sites do better buying from a shared, busy GPU than keeping their own ready.

The smallest market is one shared GPU and several members holding sats.

### 6.9 Signals

Read together, the outputs act as signals:

| Signal | Question it answers | From |
|---|---|---|
| Allocation | Hash, infer or switch off? | `u*` |
| Price | Where is spare capacity cheapest, and who should buy instead of build? | `P_m` across sites |
| Investment | Add GPU capacity or add a miner? | Traded price against `P(1)`; `u` against `u*` over time |
| Timing | Spend now or defer? | `G` |

Only part of the timing signal is knowable in advance: the subsidy schedule is fixed; difficulty and efficiency are not.

## 7. Reference conditions

Two results are comparable only if every field below matches.

**Inference**

| Field | Value |
|---|---|
| Model | _TBD_ (open weights) |
| Weights file hash (SHA-256) | _TBD_ |
| Quantisation | _TBD_ |
| Runtime and version | _TBD_ |
| Prompt set | _TBD_ (fixed file, hash recorded) |
| Output length per prompt | _TBD_ tokens |
| Temperature | 0 |
| Concurrency | 1 (single stream) |
| Hardware | _TBD_ |

**Mining**

| Field | Value |
|---|---|
| Miner model | _TBD_ |
| Firmware and power setting | _TBD_ |
| Hashrate basis | Pool-accepted, not nameplate |

**Both**

- Energy is measured at the wall (AC), for the whole machine.
- Ambient temperature is recorded.

## 8. Measurement procedure

**Inference**

1. From off, start the machine and load the model. Record `t_start` and `J_start`.
2. With the model loaded and no job running, record mean power for 10 minutes. This is `P_ready`.
3. Run the prompt set in a loop for at least 30 minutes. Record mean power (`P_work`) and total output tokens divided by run time (`r`).

**Mining**

1. Run at the stated power setting for at least 24 hours.
2. Record total energy and mean pool-accepted hashrate.
3. Report `E_h` as joules divided by terahashes accepted.

Any figure taken from a datasheet instead of a meter is labelled *nameplate*.

## 9. Published series

| # | Series | Unit | Source |
|---|---|---|---|
| 1 | Hashing yield, by miner | sats/kWh | On-chain data and `E_h` |
| 2 | Inference state powers and rate, by hardware and model | W, tokens/s | Measured |
| 3 | Parity curve `P(u)` | sats/MTok | Series 1 and 2 |
| 4 | Price of availability `A` | sats/hour | Series 1 and 2 |
| 5 | Market price of the same model | sats/MTok | Public API prices; a fiat cross, labelled as such |
| 6 | Break-even utilisation `u*` at the market price | ratio | Series 1, 2 and 5 |
| 7 | Storage ratio `G` from each past epoch to the present | ratio | History of series 1 and 2 |
| 8 | Marginal price `P_m` | sats/MTok | Series 1 and 2 |
| 9 | Traded price, where trades occur | sats/MTok | Recorded trades |

Series 1–4, 7 and 8 are the model and depend on no fiat price. Series 5–6 are context. Series 9 is the observed rate, and is meaningful only alongside the band it fell within. Series 7 needs at least two epochs of data, so the history of series 1 and 2 must be kept from the first measurement.

## 10. Worked example

Illustrative only. All three inference figures are assumptions, not measurements.

| Input | Value |
|---|---|
| Network hashrate | ~1.16 ZH/s (`W` ≈ 6.96 × 10¹¹ TH per block) |
| `R` | ~3.15 × 10⁸ sats |
| `E_h` | 23 J/TH |
| `P_work` | 250 W |
| `P_ready` | 60 W |
| `r` | 40 tokens/s |

Hashing yield:

```
S_J = 3.15×10⁸ / (6.96×10¹¹ × 23) ≈ 1.97×10⁻⁵ sats/J   (≈ 71 sats/kWh)
```

Parity curve:

| `u` | `E_t(u)` (J/kTok) | `P(u)` (sats/MTok) |
|---|---|---|
| 1.00 | 6,250 | 123 |
| 0.50 | 7,750 | 153 |
| 0.25 | 10,750 | 212 |
| 0.10 | 19,750 | 389 |
| 0.05 | 34,750 | 685 |

Price of availability:

```
A = 1.97×10⁻⁵ × 60 × 3600 ≈ 4.3 sats/hour
```

Break-even utilisation at `p = 246` sats/MTok, twice `P(1)`:

```
u* = 60 / (246 × 40 / (10⁶ × 1.97×10⁻⁵) − 190) ≈ 0.19
```

Storage ratio, across one halving:

| Case | Network factor | Efficiency factor | `G` |
|---|---|---|---|
| Halving only; difficulty and efficiency unchanged | 2 | 1 | 2 |
| Halving, and joules per token also halve | 2 | 2 | 4 |
| Halving, but difficulty falls by a quarter | 1.5 | 1 | 1.5 |

Trading band, for a seller in the ready state and a buyer who would otherwise self-produce at 5% utilisation on the same hardware:

```
P_m    = 1.97×10⁻⁵ × (250 − 60) / 40 × 10⁶ ≈ 94 sats/MTok
P(0.05)                                    ≈ 685 sats/MTok
```

Any price between 94 and 685 sats/MTok leaves both better off.

## 11. Known limits

- **Energy is not the whole cost.** Hardware depreciation dominates the cost of inference, and the model treats energy as surplus. With depreciation or a paid tariff included, mining can run at a loss too. `P(u)` is an energy-parity price, not a full cost or a market price.
- **The sats floor is a seller's floor.** It binds only for a seller whose alternative is hashing. As the subsidy shrinks, that alternative weakens, and the floor is increasingly set by capital cost and the joule's next-best use.
- **The signals rest on trust.** Utilisation and power figures are self-reported, and tokens cannot be verified. This may be acceptable inside a community; it is not between strangers.
- **The timing signal can work against the market.** If every holder defers, nobody buys, utilisation falls, and ready GPUs go negative. What limits this is that the need for an answer is perishable too.
- **Demand enters only as `u`.** The model does not predict utilisation; it shows what follows from it.
- **Tokens are graded, not fungible.** A token from one model is not a token from another, and tokenisers differ. Every figure is quoted per named model; no quality adjustment is attempted.
- **Hashes self-verify; tokens do not.** Mining is standardised by protocol, inference only by the conventions in §7. This asymmetry is a finding of the model, not a defect to be engineered away here.
- **Hashing yield is an expectation.** Pooled, it is smooth but involves a counterparty. Solo, it needs no counterparty but is a lottery.
- **Single stream only.** Batched or concurrent serving raises `r` and lowers the curve; it would be a separate reference condition.
- **The reference model will age.** See §12.

## 12. Versioning

The reference conditions are versioned (`ref-1`, `ref-2`, …). When the reference model is replaced, both versions are measured side by side for at least one difficulty epoch so the series can be linked.

## 13. Next steps

1. Fill in §7.
2. Measure `P_ready`, `P_work`, `r` and `E_h` on the same site.
3. Publish series 1–4 for that single data point.

## Changes from 0.3

- Added the trading band (§6.8): the seller's marginal price `P_m` as lower bound, the buyer's own `P(u_b)` as upper bound.
- Added signals (§6.9).
- Added series 8 and 9, a trading-band example, and two limits (trust, deferral).

## Changes from 0.2

- Added the time dimension (§6.7): the joule floor as invariant, the two routes for a joule, and the storage ratio `G`.
- Stated the model's assumptions in §2: surplus solar energy, sats as the only unit of payment, difficulty taken as given.
- Added series 7 and a storage-ratio example.

## Changes from 0.1

- Added stock and flow (§3) and machine states (§4).
- Replaced the single efficiency figure with `E_t(u)`, and the single parity price with the curve `P(u)`.
- Added price of availability `A` and break-even utilisation `u*`.
- Measurement now records `P_ready`, `P_work`, `r` and start-up cost separately.
