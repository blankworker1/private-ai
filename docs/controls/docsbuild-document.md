# private-ai — Transparent Controls: Build Document

How private-ai turns each layer of an AI into a control the household can see, understand and change, mapped onto Ollama and Open-WebUI. Companion to the three mockup boards on the [Every AI Is Layers canvas](https://claude.ai/artifact/Avja2tTTkTaGZ9jCRV3jT9). The images below are snapshots; the sliders and toggles only work on the canvas.

## Principles

Every control follows five rules. They are what make these teaching tools rather than settings.

1. **Recognise, understand, modify.** Each control names its layer, says where you notice it in a conversation, and then lets you change it.
2. **Every setting is readable text.** Sliders and toggles compile into plain sentences that anyone in the house can read. No hidden state.
3. **Suggestions, never automatic changes.** The system may propose a change; a person presses Apply. The AI never reshapes itself.
4. **Personal by default, shared by choice.** Notes and annotations stay with their author unless they choose to share them with the household.
5. **Nothing leaves the box.** All settings, notes and logs live on the Brain PC and in the Shared Library, versioned like any other file.

## The stack

The eight layers already exist in the Phase 1 stack; the work is making each one visible. Open-WebUI's Workspace "model" preset bundles most of them in one place: base model, system prompt, attached knowledge, name and picture.

| Layer | Held by | Where it lives |
| --- | --- | --- |
| Theatre | Brain PC | Hardware, Docker, Tailscale access rules |
| Actor | Ollama | The installed model files |
| Rehearsal | The model's maker | Inside the weights; later, household fine-tuning |
| Director's notes | Open-WebUI + Shared Library | Ratings and annotations, exported to the corrections file |
| Research | Shared Library | Knowledge collections attached to the preset |
| Script | Open-WebUI preset | System prompt, mirrored as a file in the Library |
| Voice & accent | Open-WebUI preset | Compiled into the system prompt; spoken voice later |
| Costume | Open-WebUI preset | Name and picture, or the plain tilde |

![Every AI is layers: the eight layers with cloud AI and private-ai sliders side by side](img/every-ai-is-layers.png)

## Controls by layer

Most controls ride on what Open-WebUI already does; three need custom work. "Built in" means available in Open-WebUI or Ollama today, to be confirmed on install (see the last section).

| Layer | Control | Built in | Needs adding | Phase |
| --- | --- | --- | --- | --- |
| Voice & script | D, L, C, T sliders, language, house rules; script shown live | System prompt in a model preset | Slider page that compiles the prompt | 1 |
| Actor | Model picker; side-by-side answers | Model switching; multi-model chat | Household test file and saved results | 1 |
| Research | Choose which Library collections a preset reads | Knowledge attached to presets | Nothing for a start | 1 |
| Theatre | Brain status; hardware details; off switch | — | Brain-status widget (already designed) | 1 |
| Thinking | On/off toggle; "show working" fold | Collapsible thinking blocks for models that support it | Toggle in the attribute strip | 1 |
| Director's notes | One-tap labels on selected text; optional correction | Per-message rating with reason and comment | Span-anchored annotations; export; suggestion rules | 1 collect, 2 use |
| Rehearsal | Corrections counter; "retrain" shown but disabled | — | Fine-tuning pipeline | 2+ |
| Costume | Name, picture, or plain tilde | Preset name and picture | Nothing | 1, built last |

## Voice & script

The sliders are a friendly way to write the system prompt. They store numbers, the numbers pick sentences, and the sentences are the script.

![Voice & script board: sliders and house rules writing the script live](img/voice-and-script.png)

**How it works**

1. Each slider (Directness, Length, Caveats, Tone) has five steps; each step maps to one fixed sentence, as on the Voice & script board. Language and house rules add one sentence each.
2. A small page compiles the settings into the system prompt and shows it live, each line tagged with the control that wrote it.
3. On save, a deterministic script writes two things: the prompt into the Open-WebUI preset, and a copy into the Shared Library as `script/household.md`, with the slider values at the top. The Library file is the source of truth; git history shows every change.
4. Anyone can still edit the script text by hand. A hand edit marks the sliders "custom" rather than overwriting the text.

**Rules**

- The sentence tables are themselves a Library file, so the household can reword what "Answer first" actually says.
- Each change records who made it and when, so the attribute strip can show "Last change: Length −1, two days ago".
- One household default preset at first. Per-person presets come later, if the trial shows people want different voices.

## Annotations & director's notes

Annotations are the household's main way to steer the AI: a note on a specific piece of a reply, routed to the layer that can fix it.

![Chat page: attribute strip, thinking toggle, show working, an annotation in progress and a suggestion](img/chat-page.png)

**The record.** One line per note, kept in `corrections/` in the Shared Library as dated JSONL files:

| Field | Example |
| --- | --- |
| date | 2026-12-03 |
| author | the account that wrote it |
| shared | false until the author shares it |
| conversation, message | Open-WebUI ids |
| span | the highlighted text |
| label | vague · wrong · deflected · too long · good |
| note | "what should it have said?" (optional) |
| actor | the model that wrote the reply |
| script version | the script file's git commit at the time |

**Where each label goes**

- *Wrong / doesn't know* → the Library: prompts "add a document?"
- *Vague, too long, deflected* → voice & script: counted for suggestions.
- *Every note* → director's notes, the future fine-tuning data.

**Suggestions are rules, not AI.** A plain counting script checks the week's shared notes, for example "deflected ≥ 5 → suggest Caveats −1 and add the 'no consult a professional' rule". The rules live in a Library file the household can read and edit. A suggestion shows the exact script change and does nothing until someone presses Apply.

**Privacy.** Notes are personal until shared. Only shared notes count towards suggestions and the household corrections file. Recording the actor and script version keeps old notes meaningful after either changes.

## Thinking and show working

Thinking is a dial in the attribute strip; its trade-off is time on the household's single GPU.

- **Toggle.** Off by default for quick questions; on for harder ones. The strip shows "On · slower" when it's active, because everyone else's questions wait behind it.
- **Show working.** For models with a thinking mode, the raw reasoning appears in a collapsed fold under the reply, in full, not summarised.
- **The label.** The fold always carries one line: "The model's own account of its reasoning. Useful, but testimony, not proof."
- **Models without thinking.** The toggle shows as unavailable for that actor rather than disappearing, so the difference between actors stays visible.

## Production trail

Any task that makes something has four stages, and each can vary on its own. The production trail shows them in order, so the household can check the work, not just the description of it.

1. **Thinking:** the rehearsal notes. How the model understood the ask and planned.
2. **Actions:** building the set. Each tool step: what was searched, which Library files were read, what code ran.
3. **Reply:** the performance. The answer text.
4. **Artifact:** the set that stays after the show. The file, diagram or page.

**Design**

- One expandable trail under any reply that produced an artifact; each stage collapsed to one line until opened.
- Every stage can be annotated. A note on a step ("it read the wrong Library folder") points to the cause, which a note on the reply cannot.
- The trail's reminder: check the artifact, not the description. Where the reply describes the artifact, the two sit side by side.
- Re-running a task shows both trails, making clear which differences come from chance.

**Fit with Phase 1.** Shape B keeps the AI read-only, so in Phase 1 the actions stage will mostly show Library reads. The trail is designed now so it is ready when tools are added, and it keeps those tools visible from the first day they exist.

## Actor comparison and the household test

Same costume, different actor: the same question to two models, side by side, shows what the actor contributes.

- **Ask twice.** Each model answers each question twice. Differences between a model's two runs are chance; differences that hold across runs are the actor.
- **The question set.** `model-test-questions.md` in the Library (see `model-selection.md` §4), run whenever a model is added or changed.
- **Results.** Saved as dated snapshots in `model-tests/`, with the script version used, so later runs can be compared fairly.
- **The reference actor.** One fully open model, such as Olmo, kept installed for comparison, swapped in when needed.

## Build order

Start with what changes everyday experience most and costs least; costume comes last on purpose.

1. **Voice & script.** Slider page, script compiler, Library copy. Biggest effect on daily use, plain text, easy to undo.
2. **Actor comparison.** Model picker plus the household test with two runs per model.
3. **Attribute strip and thinking toggle** on the chat page.
4. **Annotations.** Start collecting from day one with Open-WebUI's built-in ratings; add span-anchored notes and the export next.
5. **Suggestion rules.** Once a few weeks of shared notes exist.
6. **Production trail.** When the AI gains any tools beyond reading the Library.
7. **Costume.** Name, picture or plain tilde.
8. **Rehearsal.** Fine-tuning, once the corrections file holds a few hundred good notes. Phase 2 or later.

## To verify on the Brain PC, and open questions

These capabilities are described from general knowledge of Open-WebUI and Ollama, not checked against the installed versions. Confirm each one on install.

- [ ] Workspace model presets hold base model, system prompt, knowledge, name and picture, and can be updated through the API
- [ ] Per-message ratings with reason and comment, and how feedback is exported
- [ ] Add-on buttons under messages (Open-WebUI "Actions"), for the annotation entry point
- [ ] Thinking blocks shown collapsed for the installed models; which ones have a thinking mode
- [ ] Whether token probabilities are available through Ollama, for a later "what it almost said" view
- [ ] `ollama show --modelfile` displays each model's template and default system prompt

**Open questions**

- [ ] Span-anchored notes: an Open-WebUI add-on, or a small custom front end?
- [ ] One household script, or one per person?
- [ ] Who can press Apply on a suggestion that changes the shared script?
- [ ] How long are personal, unshared notes kept?
