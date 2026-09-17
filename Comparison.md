# private-ai

**Your data is segregated by design. No trust required. Upgrade your AI model without needing to check the terms.**

This document explains what that sentence actually means — not as marketing, but as a description of two genuinely different mechanisms.

---

## How SaaS AI handles your data

Across every major consumer AI service — ChatGPT, Claude, Gemini, Grok, Copilot, Perplexity — the pattern is consistent: **consumer and personal-paid tiers train on your conversations by default**, unless you find and enable an opt-out setting yourself. Business, Enterprise, and API tiers generally don't train by default — the protection tracks account type, not how much an individual pays.

A few things worth knowing about how this actually works in practice, not just in theory:

- **The setting is a policy choice, not a structural limit.** The infrastructure is fully capable of training on your data either way — "off" means a switch was flipped, not that a pipeline doesn't exist.
- **Policies change.** Claude itself is the clearest example: it built an early reputation on *not* training on conversations, then in August 2025 shifted to requiring consumer users to make an active choice. Anyone who didn't respond to the prompt in time was defaulted into training and multi-year retention. The rules a person agreed to can be different a year later, through no action of their own.
- **Opt-out has a ceiling.** Even with training disabled, most providers — Anthropic included — keep a carve-out: conversations flagged for safety review can still be used for model training regardless of a stated opt-out preference. Anthropic clarified this explicitly in a June 2026 privacy policy update.
- **Retention and training are different things.** Even when training is off, conversations are typically still retained — readable, stored, subject to review — for some period before deletion, for safety monitoring.

None of this makes any single provider uniquely bad. It's the shared shape of the SaaS model: your data sits inside a system whose relationship to your privacy is a *policy*, actively managed and re-checked, provider by provider, account type by account type, indefinitely.

## How private-ai handles your data

private-ai has no equivalent setting to check, because there's no equivalent mechanism to disable. Split into two separate channels:

**Channel A — model capability flows in, freely.** Ollama's base model can be swapped for a newer, better one whenever one is released — trained by whoever built it, on their own data, at their own scale. That capability lands on the household's or node's own hardware and immediately benefits everyone using it. This channel is fully open, and using it is encouraged.

**Channel B — your data flows out into the model — does not exist.** Not disabled. Not opted out. Absent. There is no fine-tuning loop, no training pipeline, nothing in the stack through which a Shared Library file could ever become part of a model's weights:

- Ollama loads a pre-trained model file and generates answers from it.
- `oikb` and the knowledge base place *retrieved* text into a temporary prompt context for one conversation, then discard it.
- Nothing writes back. The data and the model's weights are separate files on the same disk that never computationally touch.

**Deletion is real deletion, not a request.** Remove a file from the Shared Library, let `oikb` sync, and it's gone from what the AI can surface — completely, immediately, with no residual statistical influence, because nothing was ever absorbed into weights in the first place.

## A third model, worth naming explicitly

The comparison above draws a two-way line: a SaaS provider you must trust, or private-ai's Library, which needs no trust because there's no remote party in the loop at all. There's a real third category sitting between them, and it's worth naming rather than leaving the binary framing to look unaware of it â€” **confidential computing cloud** platforms (Tinfoil is the clearest current example), which replace "trust our privacy policy" with "trust our cryptography and our hardware," while still being a remote, paid, vendor-operated service underneath.

**How it works:** specialised GPUs (NVIDIA Hopper/Blackwell in confidential-computing mode, similar approaches from AMD and Intel) run your AI workload inside a hardware-isolated "secure enclave." Data is encrypted in memory and only decrypted inside that boundary â€” in principle invisible even to the cloud host and the platform operator. Cryptographic attestation lets you verify, rather than just trust, that the enclave is running the exact code it claims to be running. That's a genuinely stronger claim than an ordinary SaaS privacy policy: "we promise not to look" becomes "we cryptographically cannot look, and you can check."

**What doesn't change, underneath the stronger guarantee:** it's still someone else's hardware, in someone else's facility, accessed as a paid, ongoing service. The privacy claim now rests on trusting a hardware vendor's implementation of confidential computing and the attestation protocol verifying it correctly â€” a narrower, more technical form of trust than an ordinary SaaS relationship, but trust nonetheless. It doesn't remove the question the way owning the hardware does; it answers the question more rigorously while leaving it a question.

## Three models, side by side

| | Typical SaaS AI | Confidential Computing Cloud | private-ai |
|---|---|---|---|
| **Where your data goes** | Provider's servers | Provider's servers (hardware-isolated enclave) | Never leaves your own hardware |
| **What guarantees privacy** | A policy you agree to | Cryptographic attestation of hardware behaviour | Nothing to guarantee â€” no remote party exists |
| **What you must trust** | The company, its policy, its future decisions | A hardware vendor's TEE implementation + the attestation chain | Nothing â€” physical absence, not a promise |
| **Ongoing cost** | Recurring subscription | Recurring, paid usage | One-time (or financed) hardware purchase |
| **What happens if you stop paying** | Access ends | Access ends | Nothing â€” you already own it |

## Where each genuinely fits

Not every use case is private-ai's use case, worth saying plainly rather than implying otherwise. Confidential computing cloud is the right tool specifically for workloads too large for any home or small-node GPU to run at all, where cloud-scale compute is a hard requirement and strong data-in-use guarantees still matter â€” a case private-ai deliberately doesn't try to serve. private-ai's answer is different in kind, not just degree: for a household or a coop's actual working scale, the question of what to trust doesn't need a stronger answer â€” it needs to stop being a question, because there's no remote system in the loop to trust at all.

---

## Why "no trust required" is a precise claim, not a slogan

The distinction is structural, not promissory. A SaaS provider saying "we don't train on your data" is a policy sitting on top of infrastructure fully capable of doing exactly that — which is how Anthropic's own 2025 shift happened, and why the safety-flag carve-out exists even for people who'd opted out. There was a mechanism, and a decision was made about how to use it.

private-ai has no mechanism to make a future decision about. The one-way channel isn't a promise being kept — it's an absence being maintained by the architecture itself. Changing it would mean someone building an entirely new training pipeline from scratch, not flipping a setting.

That's what makes upgrading the model a non-event: no new terms of service to read, no consent flow to click through, no policy that might have quietly changed since last time. A better model arrives, gets swapped in, and nothing about what the household already stored is affected either way.

**One honest limit, stated plainly rather than smoothed over:** "no trust required" is specific to training and data exposure — it doesn't mean zero trust in anything. The household still trusts that the open-source code is what it claims to be, and trusts its own hardware. Narrow and true is the point, not an unqualified absolute.

---
*Addendum drafted [date: 2026-09-17], prompted by review of a confidential-computing cloud platform (Tinfoil) whose own marketing draws the same "policy-based trust vs. verifiable privacy" distinction this document already makes â€” evidence the two-way framing needed a third category named, not a reason to change the existing argument.*

