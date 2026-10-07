# Trade AI — Stack Spec

**Draft v1** — 2026-10-07

A fourth use case for private-ai, after Home AI, Community/Coop AI and the exhibition. Concept stage: nothing built. Deferred behind the Home AI trial and the exhibition (§12).

**Companions:** [model.md](model.md) (the joule model that prices inference in sats), [protocol.md](protocol.md) (the market between nodes). This file covers only how Trade AI runs on a private-ai node, and how it is kept apart from everything else on that node.

---

## 1. Concept

A private-ai household that also has solar and a bitcoin miner has two flexible loads on one sun. The miner takes whatever is spare and turns it into sats. The AI machine earns more per joule, but only when someone is asking. Trade AI lets a node sell inference it is not using, and buy inference it cannot produce, from other nodes like it, in sats.

One sun, two loads, one unit.

It is not a business and is not expected to pay for the hardware. The realistic reasons to run it are, in order: occasional access to a larger model on someone else's machine; a lower cost of the household's own AI use; a use for idle capacity; independence from large providers. See `model.md` §6.8 and `protocol.md` §3.

## 2. Where it sits

| | Home AI | Exhibition | Trade AI |
|---|---|---|---|
| Who uses the AI | Named household accounts | Walk-in visitors, facilitated | Other nodes, unattended |
| Reached over | Tailscale only | Tailscale | Public internet, outbound-only tunnel |
| Payment | None | Lightning, unlocks a timed session | Lightning, metered per token |
| Library access | Shared Library | None | None |
| Stack | Home | Exhibition | Trade |

Trade AI reuses three things the project already has: the Power Control Node's telemetry, the exhibition's Lightning payment layer, and the exhibition's isolated-stack pattern.

## 3. The exceptions it makes

Trade AI cuts across five standing principles. Each exception is deliberate and bounded. If a bound cannot be met, Trade AI does not run.

| Principle | What Trade AI does | Bound |
|---|---|---|
| No one selling seats | Sells spare inference to outsiders | The household always has priority (§7). Outside buyers are never accounts and never count as seats |
| No open ports, ever | Accepts requests from the public internet | Outbound-only tunnel; no router port opened. Only the gateway is reachable, never the model server (§6) |
| Nothing leaves the hardware | Buying sends a prompt to another node | Buying is a separate, explicit action. No Library content is ever attached (§8) |
| A human still acts | Trades complete without sign-off | A person sets the policy. Deterministic code does the rest. No AI holds the wallet or decides a trade (§9) |
| One job at a time, full power | Adds outside jobs to the same GPU | Outside jobs are capped in length and yield to the household (§7) |

This is the second exception to "no open ports", after the public mirror, and it follows the same rule: the exposed part has no path back into anything private.

## 4. Node hardware

| Item | Role | Status |
|---|---|---|
| Solar PV and battery (Victron GX) | Energy source and telemetry | Existing where present |
| Bitcoin miner, curtailable | Base load; sets the sats value of a joule | Existing where present |
| Power Control Node | Senses PV, battery and miner; runs the quote engine | Specified in `spec-mb-pcn.md`; extended here |
| AI machine | Serves inference | Brain PC or Micro Brain PC |
| Energy meter on the AI machine | Measures ready and working power | New, small |
| Lightning wallet | Receives and makes payments | Pattern from the exhibition spec |

The two AI machines are two different grades, which is what gives nodes a reason to trade:

| Machine | Model class | Likely role |
|---|---|---|
| Brain PC (RTX 5060 Ti, 16 GB) | Mid-size models | Sells to smaller nodes |
| Micro Brain PC (Jetson Orin Nano, 8 GB) | About 4B parameters | Buys larger-model work; sells small-model work |

## 5. Components

| Component | Runs on | New? | Deterministic |
|---|---|---|---|
| Telemetry and control | Power Control Node | Existing design | Yes |
| Meter (power, tokens served) | Smart plug and gateway | New | Yes |
| Quote engine | Power Control Node | New | Yes |
| Model server | AI machine | Existing | — |
| Inference gateway (payment, metering, receipts) | AI machine, Trade stack | New | Yes |
| Wallet | AI machine, Trade stack | Exhibition pattern | Yes |
| Publisher (signed listings) | Power Control Node | New | Yes |
| Buyer client (policy, routing, spot-checks) | AI machine, Trade stack | New | Yes |
| Board | Elsewhere; a viewer only | New | Yes |

The quote engine sits with the Power Control Node because both are fixed-rule automation: sensed state in, a formula applied, a number out. No AI judgment is involved in quoting, paying or checking.

## 6. Isolation

Trade AI is a third Docker Compose stack, beside Home and Exhibition.

- Its own containers and its own data volumes.
- No Open-WebUI library, no `oikb`, no mount of the Shared Library or any Personal Layer data.
- Model weight files are shared underneath, as in the exhibition spec. They carry no household content.
- The gateway is the only thing reachable from outside, through an outbound-only tunnel. The model server's port is never exposed.

**Running alongside Home AI.** The exhibition stack replaces the Home stack while it runs. Trade AI is meant to run at the same time, which raises a question the exhibition did not have. Three options:

| Option | How | Trade-off |
|---|---|---|
| A. Dedicated machine | Trade AI runs on its own Micro Brain or second PC | Cleanest isolation; household Brain untouched; costs a machine |
| B. Shared model server | One model server; Home and Trade are separate front ends with a priority rule | No extra hardware; outside prompts pass through the same server process as household prompts |
| C. Time windows | Trade stack is up only when the Home stack is down | Simple; household has no AI during trade windows |

Two model servers each loading the same model would need the memory twice, which a 16 GB card does not have. That rules out the naive version of running both stacks side by side.

**Leaning:** option A for any first public trial, for the same reason the exhibition leans towards a dedicated PC. Option B only after it has been shown that nothing from a household prompt can be observed from the Trade side.

## 7. Household priority

Applies to options B and C; with option A the household is unaffected.

- A household request always goes ahead of any outside request.
- An outside job that has started may not be cleanly interruptible. Job length is therefore capped, so that the household never waits longer than a set time. The cap is a seller parameter (`protocol.md` §6).
- Load `ρ` in the quote counts household use. A busy household makes the node expensive, then off-market.
- Selling happens only in availability windows. The default follows the sun, which often coincides with an empty house.
- No outside buyer is given an account, a login or a place on the Tailscale network.

## 8. Buying

- Buying is a separate entry point, not part of the Home AI chat. A request sent out is plainly marked as leaving the house.
- No Library content, retrieval result or prior conversation is attached. The buyer client sends only what it is given.
- Home AI's assistant never initiates a purchase.
- Where buying is automated, it is done by the buyer client under a human-set policy (`protocol.md` §9), for jobs a person or a script has queued.
- The seller can read the prompt. Trade AI is for work that is not private.

## 9. Wallet and policy

- Self-hosted wallet in the Trade stack, following the exhibition's LNbits pattern.
- A spending wallet separate from any savings, holding a small balance.
- Policy, set by a person: spending caps per increment, hour and day; ceiling price and allowed list per grade; battery reserve; availability windows; minimum ask; maximum job length.
- A stop switch that takes the node off-market at once.
- Receiving payments needs inbound Lightning capacity. This is likely the largest practical hurdle for a new node and is undecided (§13).

## 10. Reporting

Trade AI reports into the household's Library by the existing Shape B pattern: a deterministic script writes a snapshot, `oikb` ingests it, and the AI only ever reads it.

The snapshot covers quotes published, jobs sold and bought, sats earned and spent, energy used in each machine state, and the current parity figures from `model.md`. The household can then ask its own AI how the node is doing, with no live link between the AI and the trading code.

## 11. Measurement

`model.md` has no measured inputs yet. The Home AI trial is the place to get them, at almost no cost: put an energy-monitoring plug on the Brain PC from day one.

| Quantity | From |
|---|---|
| `P_ready`: power with the model loaded, no job | Plug |
| `P_work`: power while generating | Plug |
| `r`: tokens per second | Model server |
| Start-up time and energy | Plug |
| `E_h`: miner joules per terahash | Power Control Node and pool |

Figures already recorded in `hardware-power.md` give a first check:

| Item | Idle | Peak |
|---|---|---|
| Brain PC | 60–80 W | 280–320 W |
| Starlink | 50–65 W | 75–130 W |
| Router | 10–15 W | 15 W |

If Starlink and the router are on only to serve outside jobs, the cost of being available is nearer 120–160 W than 60–80 W. Where they are on for the household anyway, only the AI machine counts.

## 12. Stages

| Stage | Result | Depends on |
|---|---|---|
| 0 | Measured inputs for `model.md` | Home AI trial, with the energy plug |
| 1 | One node computes and displays its own quote; no trading | Power Control Node built |
| 2 | Two nodes trade one grade with prepaid increments | Exhibition's Lightning layer working |
| 3 | Signed listings on relays, and the board | Stage 2 |
| 4 | Spot-checks and reputation | Two nodes on one grade |

Stage 1 is useful without any trading: it tells a single site whether a joule is better spent hashing, inferring or not at all.

## 13. Open decisions

- **Dedicated machine or shared model server** (§6).
- **Whether an outside job can be interrupted**, and so how long the cap in §7 needs to be.
- **Lightning setup**: own node, or a wallet whose liquidity is managed by a provider.
- **Tunnel provider** for the outbound-only endpoint, and whether it is the same as the public mirror's.
- **Which grades to offer first**, and the reference conditions in `model.md` §7.
- **Whether the quote engine runs on the Power Control Node or beside it**, given that node's hardware class.
- **Legal position** of selling a service for bitcoin from a household, in Italy and in the UK. Not yet looked at.
- **Licence** for `model.md` and `protocol.md`. They inherit AGPL-3.0 from the repo; a standard meant for wide adoption may suit a more permissive licence.

## 14. Status

Concept. Model at v0.4, protocol at v0.2, this spec at v1. Nothing built, nothing measured. `model.md` does not yet include the energy state factor defined in `protocol.md` §5.2 and needs bringing into line.

---

*v1 — first version of this document, compiled from working discussion. [date: 2026-10-07]*
