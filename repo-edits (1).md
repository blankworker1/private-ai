# Proposed edits for moving Trade AI into private-ai

Not a file for the repo. A checklist of changes to make by hand, with the wording to paste. Written against the private-ai repo as it stood on 2026-10-07.

## 1. Add the folder — done

`trade/` is in the repo with its three files, matching the prepared versions:

```
trade/
  spec-trade-ai.md
  model.md
  protocol.md
```

The four context files are not included. They stay in the archived ai-btc-x repo.

**Remove `repo-edits.md` from the repo root.** It was uploaded with the others but is a working checklist, not a project document.

## 2. `DECISIONS.md`

Add under **Architecture**:

```
- **Trade AI is a separate, isolated stack — never a feature of Home AI** — selling and buying inference between nodes cuts across "no open ports", "no one selling seats" and "nothing leaves the hardware"; each exception is bounded, and Trade AI does not run if a bound cannot be met. `trade/spec-trade-ai.md` §3
- **Household priority is absolute over outside buyers** — outside buyers are never accounts or seats; outside jobs are capped and yield to the household. `trade/spec-trade-ai.md` §7
- **Buying inference is explicit and carries no Library content** — a separate entry point, plainly marked as leaving the house; Home AI's assistant never initiates a purchase. `trade/spec-trade-ai.md` §8
- **No AI in the trading path** — quoting, paying, checking and recording are deterministic code under a human-set policy; no AI holds the wallet. Same line as the Power Control Node. `trade/spec-trade-ai.md` §5, §9; `trade/protocol.md` §9
- **Trade AI deferred** behind the Home AI trial and the exhibition — the exhibition builds the Lightning-for-inference layer first. `trade/spec-trade-ai.md` §12
```

Add under **Hardware — Phase 1**:

```
- **Energy-monitoring plug on the Brain PC from day one** — the Home AI trial then produces the measured inputs the Trade AI model lacks, at almost no cost. `trade/spec-trade-ai.md` §11
```

Add under **Software & Data**:

```
- **Inference priced in sats through the joule** — hashing yield in sats per joule, times joules per token, gives a parity price that needs no fiat price. `trade/model.md` §6
- **Quotes are computed, not set** — a seller chooses a margin; the rest comes from the chain, the machine's load and the site's energy state. `trade/protocol.md` §5
```

## 3. `README.md`

**In "Why this, not a subscription"**, replace the last bullet with:

```
- **No open ports, ever** — remote access is a private Tailscale network throughout. There are two deliberate, separately-scoped exceptions, each isolated from everything private: a public mirror of already-decided-to-be-public archive content, with no AI assistant on the public side; and Trade AI, a deferred, opt-in stack for trading spare inference between nodes.
```

**In the same list**, add a sentence to the end of the "Immune to the failure mode that ruined shared broadband" bullet:

```
Trade AI, where a household chooses to run it, sells only capacity the household is not using, and outside buyers are never accounts.
```

**In "Project structure"**, add after the Phase 2 paragraph:

```
**Trade AI (deferred).** A separate, isolated stack that lets a node with solar and a bitcoin miner sell inference it is not using, and buy inference it cannot produce, from other nodes in sats. Sequenced after the Home AI trial and the exhibition.
```

**In "Documents"**, add a new table:

```
**`/trade` — Trade AI (deferred):**

| File | What it is |
|---|---|
| `trade/spec-trade-ai.md` | **Trade AI** — the stack on a node: isolation, household priority, the exceptions it makes and how each is bounded. |
| `trade/model.md` | The joule model — prices inference in sats with no fiat price; parity curve, price of availability, break-even utilisation, storage ratio. |
| `trade/protocol.md` | The market between nodes — computed quotes, signed listings, streamed Lightning payment, proof of grade by spot-check. |
```

## 4. `implementation-checklist.md`

Add one item:

```
- [ ] Fit an energy-monitoring plug to the Brain PC before first power-on. Record power with the model loaded and idle, power while generating, and tokens per second. (`trade/spec-trade-ai.md` §11)
```

## 5. The old `ai-btc-x` repo

Replace its README with the text below, then archive the repo (Settings → Archive this repository). Do not delete it: it is now the only home of the four context files.

```
# ai-btc-x

This project has moved. It is now Trade AI, part of private-ai:

https://github.com/blankworker1/private-ai/tree/main/trade

The repo description to use there, if wanted:
A theoretical model for pricing AI inference in bitcoin, using the joule as the shared denominator.
```

## 6. Still to do after the move

- Bring `trade/model.md` into line with `trade/protocol.md` §5.2 (the energy state factor).
- Decide the licence question in `trade/spec-trade-ai.md` §13.
- The architecture and base-layer diagrams do not show Trade AI.
