# Remote Setup & Training Service

**Status: draft. Not offered yet.** The service opens only after the Phase 1 household trial has run (see `DECISIONS.md`, Go-to-market). Pricing is still to be decided.

---

## What it is

A paid service for households (and later co-ops) who have bought the hardware and want private-ai set up for them, or want to learn to run it themselves.

**The awkward truth first:** a remote setup service means someone from outside gets into your private AI box. That's exactly what this project says shouldn't happen without consent. So the service is built around one rule:

> **You let us in, you decide what we can reach, you see when we leave.**

---

## What's included

| Part | What happens | Done remotely? |
|---|---|---|
| **1. Pre-setup call** | Agree what the household needs: accounts, devices, which models, what goes in the Library first. Nothing is touched. | Yes (video call) |
| **2. System setup** | Install and configure the stack: Ollama, Open-WebUI, models, accounts, Tailscale access rules, backup drive, household system prompt. | Yes, under the access protocol below |
| **3. Library starter** | Set up the Shared Library structure and sync. **The household adds its own documents**; we never upload or read them. | Yes (structure only) |
| **4. Household training** | One or more sessions on: using the AI, adding to the Library, the four levers (`model-selection.md`), rating answers, backups, and the off switch. | Yes (video call) |
| **5. Handover** | Access removed, change record delivered, the household confirms it can run everything without us. | Yes |

**Optional later:** follow-up check-ins, model updates, help with the corrections dataset and fine-tuning (Phase 2).

---

## The access protocol

1. **The customer grants access, not us.** Access is given through Tailscale, using device sharing or a temporary invite that the customer creates and can revoke at any moment. No passwords are sent to us, and no permanent remote-access tools are installed.
2. **Scope: system configuration only.** We work on the operating system, Docker, Ollama, Open-WebUI settings and network access rules. We **never** open the Shared Library's contents, anyone's chats, or the Personal Layer. Where a task would require it, the customer does that step themselves with us guiding over the call.
3. **Announced sessions.** Every remote session has an agreed start and end time. The customer can watch (screen share) if they want to.
4. **Written change record.** Every change is logged in plain language (what, why, when) and delivered to the customer, kept in their Shared Library under `setup-log/`.
5. **Access removed at the end.** The share or invite is revoked at handover, and the customer confirms it in the Tailscale admin panel themselves.
6. **Nothing leaves the box.** We don't copy configs, logs or data off the customer's hardware. Reusable improvements go into this public repo as generic templates, never as customer data.

**Phase 2 (Community / Coop AI):** the same rules apply, adapted for Headscale and for the co-op's governance. The access grant is a recorded decision by the board, not one person's click.

---

## Open questions

- [ ] Pricing: fixed packages (setup only / setup + training) or hourly?
- [ ] Languages offered: English, Italian, Welsh?
- [ ] Does the service include the Micro Brain tier, or only the full Brain PC?
- [ ] Contract and invoicing: who is the service provider of record (partita IVA or a future entity)?
- [ ] What happens if a setup goes wrong: support period, refund terms?
