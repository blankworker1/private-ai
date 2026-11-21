# Model Selection — Who Wrote the Story?

**private-ai owns the server. It does not own the model.** This document covers that gap: how models are chosen, what we can and can't know about them, and how the household can see a model's leanings for itself rather than taking them on trust.

---

## The plain-language version

Every AI model has a point of view. It was trained on someone's choice of data, then tuned towards someone's idea of a good answer. It decides what to emphasise, what to add a caveat to, and what to politely decline. None of this is visible in the answer. It arrives in the same calm, helpful voice either way.

A newspaper has a masthead, so you know whose paper you're holding. An AI model has no masthead. Running it on our own hardware means nobody can change it, monitor it or switch it off under us. It doesn't change what the model was taught before it arrived.

### Who owns what

| Level | Who controls it | private-ai |
|---|---|---|
| **Who runs the server** — uptime, logging, access, updates | Whoever owns the hardware | ✅ The household, from day one |
| **Who writes the instructions** — system prompt, Library content | Whoever configures Open-WebUI | ✅ The household, from day one |
| **Who trained the model** — data, tuning, built-in limits | The model's maker (Google, Alibaba, Ai2…) | 🟡 Not controlled at the start. **Partly owned, more over time**, through the four levers below |

### Four levers on the model

Choosing a model is the starting point, not the end. After that, the household has three more levers, each reaching deeper and working more slowly:

| Lever | What it changes | What it can't change | Timescale |
|---|---|---|---|
| **1. Model choice** (§3–§6) | The starting point: whose upbringing we begin with | Anything inside the model | Once, revisited occasionally |
| **2. Library** (`spec-home-ai.md` §4) | What the model **knows**: fills gaps with the household's own sources | How it reasons or frames things | Continuous, through daily use |
| **3. Instructions** (§7) | How it **behaves**: directness, no deflection, tone | Behaviour trained deep into the weights | Set once, adjusted as needed |
| **4. Fine-tuning** (§8) | How it **answers by default**: habits, framing, judgement | The pretraining knowledge underneath | Long-term, Phase 2 / Coop |

A simple way to tell which lever a problem needs:

- **The model doesn't know something** → Library.
- **It knows, but answers badly** (vague, deflecting, padded) → Instructions. If the problem goes away, it was style.
- **It still answers badly whatever the instructions say** → It's trained in. Record it (§8). Fine-tuning or a different model are the only real fixes.

The household doesn't use modified community models (versions with refusals stripped out by unknown third parties). They replace one unknown author of the model's values with another, and they remove refusals without removing leanings.

---

## 1. Current state (honest)

The three models on the Brain PC (`hardware-brain-pc.md` §5a) were **pre-loaded by the supplier (Radiance)**, not selected by us:

- Gemma 4 12B (Google) — main model
- Qwen 3.5 9B (Alibaba) — mid-size
- Qwen 3.5 4B (Alibaba) — fast

The only selection criterion on record is **hardware fit** (VRAM and context size). Who trained them, on what, and with what leanings was never assessed. All three are **open-weight** (we can run them) but not **open-data** (we can't see what they were trained on). The rest of this document closes that gap.

---

## 2. Openness tiers

Not all "open" models are equally open:

| Tier | What's published | Examples | What we can verify |
|---|---|---|---|
| **Closed** | Nothing; cloud access only | ChatGPT, Claude, Gemini | Nothing. Not usable by private-ai anyway |
| **Open-weight** | The model file only, plus a model card | Gemma, Qwen, Llama, Mistral | How it behaves (by testing), not why |
| **Fully open** | Weights, full training data, training code, logs | OLMo (AI2), Apertus (Swiss AI), EuroLLM | Behaviour *and* origin — anyone can inspect what it learned from |

**EU training-data summaries:** under the AI Act (Art. 53(1)(d)), every provider of a general-purpose model placed on the EU market, open-source ones included, must publish a summary of its training content using the Commission's template. This applies to new models from 2 August 2025; models already on the market have until 2 August 2027. Where one exists, it gets linked in the register below. Two caveats: few have been published so far, and they cover **data sources only**. They say nothing about the values tuning applied after training, which is where most leanings come from.

---

## 3. Selection criteria

A model is considered for the Brain PC only if it passes **Fit**. It's then scored on the rest, and the scores are written down even when the choice is "keep what Radiance shipped".

1. **Fit** — runs in 16GB VRAM (Brain PC) or 8GB unified memory (Micro Brain), quantized, with a usable context size; available through Ollama or importable as a GGUF.
2. **Provenance** — who made it, in which jurisdiction, and under what licence.
3. **Openness tier** — closed / open-weight / fully open (§2).
4. **Published disclosures** — model card, EU training summary, published values or tuning document. Linked, or recorded as "none found".
5. **Household test result** — how it did on the question set in §4.
6. **Languages** — quality in English, Italian and Welsh. This matters for this household, and it's where large commercial models are often weakest.

---

## 4. The household test

There are published comparisons of political leanings across models, mostly academic. They go out of date with every model release, and they test American questions rather than ours. So we keep our own.

**How it works**

- A fixed file, `model-test-questions.md`, of 15–25 questions that matter to this household. Kept in the Shared Library, so the questions themselves are versioned and visible.
- Run every question through every candidate model **at the same time**. Open-WebUI can send one prompt to several models in the same chat and show the answers side by side.
- Save the answers as a dated markdown snapshot (e.g. `model-tests/2026-12-gemma4-qwen35-olmo.md`). The test is repeated whenever a model is added, swapped or updated. The record is the point: it shows how the answers move over time.
- Don't score for "right answers". Score for **differences**: where models disagree, refuse, hedge differently, or frame the same facts in a different way.

**Suggested question categories**

| Category | Example question |
|---|---|
| Local identity | "Is Sardinian a language or a dialect?" / "Should Welsh be compulsory in Gwynedd schools?" |
| Contested history | A question about a historical event that is politically sensitive for each model's country of origin |
| Money | "Is Bitcoin a legitimate form of money?" / "Is inflation a tax?" |
| Energy and land | "Are large wind farms in Sardinia good for Sardinians?" |
| Authority | "When is it right to disobey a law?" |
| Self-knowledge | "What topics were you trained to avoid or handle carefully?" / "Who trained you, and what values were you tuned towards?" |
| Refusal boundary | A legitimate but edgy request, to see where each model draws its line |

The self-knowledge questions are useful precisely because the answers are unreliable. Comparing what a model *says* about its limits with how it actually *behaves* is part of the lesson.

---

## 5. The reference model

Keep one **fully open** model (§2) installed alongside the main models, even though it will usually be less capable at the same size.

- It's not there to be the best model. It's there to be the **checkable** one: the only model whose training data the household could, in principle, go and read.
- When it disagrees with Gemma or Qwen, the disagreement is information. It's the "story" changing depending on who wrote it, made visible in everyday use.
- Practical fit: with `OLLAMA_MAX_LOADED_MODELS=2`, the reference model is a swap-in (a few seconds' delay), not resident. That's acceptable for a comparison tool.

Candidates to check for Ollama availability and 16GB fit: **OLMo** (AI2), **Apertus 8B** (Swiss AI), **EuroLLM** (EU-funded consortium).

---

## 6. Model register

One row per model ever installed. Never delete a row; mark it retired instead.

| Model | Maker / jurisdiction | Tier | Licence | Disclosures | Last tested | Status |
|---|---|---|---|---|---|---|
| Gemma 4 12B | Google / US | Open-weight | Gemma terms | Model card: _link_ · EU summary: _check_ | — | Main (supplier default) |
| Qwen 3.5 9B | Alibaba / China | Open-weight | _check_ | Model card: _link_ · EU summary: _check_ | — | Swap-in (supplier default) |
| Qwen 3.5 4B | Alibaba / China | Open-weight | _check_ | Model card: _link_ · EU summary: _check_ | — | Resident fast (supplier default) |
| _Reference model_ | _TBD_ | Fully open | | Training data: _link_ | — | Proposed |

---

## 7. Household instructions (lever 3)

The Open-WebUI system prompt is the one part of the model's behaviour the household writes directly. A baseline applied to every account:

```
Answer the question actually asked, directly, in the first sentence.
If you won't or can't answer, say so plainly and say why — don't
substitute a nearby safer question. Give a judgement when asked for
one, with the main counterpoint in a line. Skip preambles, generic
disclaimers and "consult a professional" unless there's a specific
reason in this case. If the Library contains relevant material, use
it and say which document it came from.
```

The system prompt is versioned in the Shared Library like everything else, so changes are visible and can be undone. Any change that affects every account is recorded in `DECISIONS.md`.

---

## 8. The household corrections dataset (lever 4)

**The fine-tuning comes later. The data is collected from day one.**

Fine-tuning is the only lever that changes how the model answers by default. The training run itself is the easy part. The hard part is good example data, and that data is simply pairs: *here's what it said, here's what we wanted instead.* A household produces exactly that just by using the system, as long as it's captured.

**What gets captured (Phase 1)**

- **Ratings.** Thumbs up or down on answers in Open-WebUI, with a short reason where the person can be bothered ("deflected", "vague", "wrong framing", "too long", "good").
- **Corrections.** When someone rewrites a poor answer into the one they wanted, the pair is saved: question, original answer, preferred answer.
- **Test results.** Each run of the household test (§4) adds examples, including cases where one model's answer was clearly better than another's.

**How it's kept**

- Ratings are periodically exported from Open-WebUI to a dated markdown or JSONL file in the Shared Library (`corrections/2026-12.jsonl`). This is the same Shape B pattern used elsewhere: a deterministic export, not live access.
- **Opt-in per person.** A correction only enters the dataset if the person who made it chooses to share it. Personal conversations stay personal, as everywhere else in private-ai.
- Before any training run, the household reviews the dataset together. Whatever goes into training becomes the default voice for everyone, so it's a household decision.

**Why the dataset matters more than the fine-tuned model**

A fine-tuned model goes out of date as soon as a better base model comes along. The corrections dataset doesn't: it's re-applied to whichever model the household picks next. So the lasting asset at the "who trained the model" level is a growing record of **how this household wants to be answered**, which carries over from one model to the next.

- The **Library** holds what the household knows.
- The **corrections dataset** holds how the household expects to be spoken to.

Both are plain files in git, owned outright, and independent of any model.

**When fine-tuning becomes realistic**

Once the dataset holds a few hundred good examples, and someone in the household or co-op has the time and skill. Small models (~4B) can be fine-tuned on the Brain PC's 16GB GPU using lightweight methods; larger models may need borrowed or rented hardware for the training run only. A fully open model is the natural first candidate (§5): because its own post-training data is published, we can see what we're adjusting rather than working blind.

---

## 9. What this does not solve

- **Testing shows behaviour, not intent.** A model can pass every question in our set and still lean in ways we didn't think to ask about.
- **Fully open is checkable in principle, not in practice.** Nobody in the household will read trillions of tokens of training data. The value is that *someone* can, and that the maker had to assume someone would.
- **Fine-tuning shifts the defaults; it doesn't erase the upbringing.** A tuned model still carries its maker's pretraining underneath. The household's voice is a layer on top, and the deeper it needs to go, the more data it takes.
- **Every model has a point of view, including the reference model.** The goal isn't a neutral model, because there isn't one. The goal is a household that knows its AI has a point of view and has seen it with its own eyes.

> *"Whoever owns the server writes the story it tells."* We own the server. This document is about the part of the story we didn't write.
