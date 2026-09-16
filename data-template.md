# Data Template for Passive Sensor/Monitoring Data

A reusable pattern for adding any external data source (solar, weather, water, anything sensor-shaped) to a node's knowledge base — without ever giving the AI live network reach or execution ability. Applies equally to a Home AI node or a Community AI node; not phase- or project-specific.

---

## Data Quality Principles

Applies to every Shape B integration, not just the worked examples below — "garbage in, garbage out" is a data-engineering problem, not an AI one, but it matters more here because nothing downstream will ever know to doubt a bad value once it's written.

- **Validate at the point of collection, not later.** The polling script should sanity-check the value *before* writing it — reject a battery SoC of 140%, a negative solar yield, an impossible timestamp. Catching a bad reading at the door is the only place it's actually catchable; once written, it's indistinguishable from a real value to both the AI and any rollup script.
- **Consistent units and ISO timestamps, always.** A firmware update silently switching kWh to Wh, or a script mixing local time and UTC, is invisible to an AI reading the files the way it might not be to a human skimming a spreadsheet.
- **Missing data needs an explicit marker, not silence or a zero.** A gateway offline for six hours should say so (e.g. a `gaps: [...]` field) — otherwise a real outage and a genuinely low-generation day look identical.
- **Idempotent writes.** A script that runs twice, or a gateway that briefly drops and reconnects, should safely overwrite the same day's file — never append a duplicate. A duplicated day quietly inflates a rollup's total until the numbers look wrong for no obvious reason.
- **Provenance in every file.** Which device, which script version collected it. Cheap now, genuinely hard to reconstruct years later when trying to understand why one stretch of data looks different from the rest.
- **Structured fields plus a short human-readable line, not one or the other.** Frontmatter serves the rollup script (§6); a plain sentence underneath ("12.4 kWh generated, battery held above 60% all day") serves the AI's direct retrieval-based answers well even before any rollup exists.

---

## 1. The Principle

**Shape B = deterministic collection, AI reads a snapshot. Never Shape A (AI has live tool-access to query or act on other devices directly).**

This isn't a style preference — it's the same "not trustless execution, a human/script acts, the AI only ever reads what's already recorded" principle already governing the Shared Library backup reminder (`spec-home-ai.md` §4a) and the Archiver Protocol's Loomio-outcome-then-push flow. Shape A would be the first thing in this entire architecture to break that rule; Shape B extends it cleanly.

**Why this matters beyond tidiness:** live tool-access means the AI needs credentials/network reach into other systems — a new capability class, and a genuine prompt-injection surface (anything the model ever processes becomes a place an instruction to "check/poll X" could theoretically be smuggled into, where today there's nothing for such an instruction to act on). Shape B has none of that: the credential lives with a deterministic script, never with the model.

## 2. The Template

**Step zero, before any script gets written: decide the schema deliberately.** This is the step most likely to get skipped, and the one most expensive to fix retroactively — a field never recorded can't be backfilled once eight months have passed without it. Treat it as its own decision, not something that evolves implicitly file by file. See §3a for how this plays out concretely.

Then four steps, identical every time:

1. **A small script polls the data source on a fixed schedule** — same `cron`/Task Scheduler pattern already used for `oikb`'s own sync and the §4a backup reminder. Daily or hourly, not live/continuous.
2. **The script writes a plain markdown snapshot** into a dedicated Shared Library subfolder, following the project's existing file conventions — dated filename, YAML frontmatter, one topic per file. The **frontmatter holds the actual structured data fields** (not just tags/date/project) — this is what makes both the rollup script (§6) and the AI able to read values reliably, rather than parsing them out of prose. Summaries, not raw high-frequency telemetry — keeps repo size sane (same discipline as the retention-policy checklist item).
3. **`oikb` ingests it on its normal cycle** — the identical pipe every other piece of Shared Library content already goes through. No new ingestion mechanism per integration.
4. **The AI answers questions from the last ingested snapshot** — ordinary retrieval, same as any other Shared Library content. It never touches the source's API directly and never holds live reach into the sensor/device.

**One design choice worth making deliberately per integration:** prefer a data source's *local* network API over its cloud API when one exists. A cloud API call is a small, ongoing dependency on someone else's server being up; a local API call over Tailscale/LAN has none. Not always available, but worth checking before defaulting to the cloud path — see §4.

## 3. Worked Example — Victron MultiPlus-II GX (Solar/Battery)

**Confirmed as the first real Shape B test device.** The MultiPlus-II GX has Venus OS built directly into the inverter/charger unit — no separate Cerbo GX needed. This makes the local path the default here, not a fallback. Still sequenced with the Phase 2 off-grid solar build (`hardware-power.md`) — see `notes.md`, not buildable until that hardware exists.

- **Data:** solar yield, battery state of charge, consumption.
- **Source: local only — no cloud VRM API needed.** Venus OS exposes this data directly over Modbus TCP or MQTT on the local network/Tailscale. No open ports, no dependency on Victron's servers being up, consistent with the project's principle throughout.
- **Snapshot:** `/energy/2026-09-15.md`, daily.
- **Question it answers:** "how much solar did we generate this week."

## 3a. Schema Design, Worked — Victron Field Selection

The MultiPlus-II GX can report dozens of Modbus registers — most will never get asked about. Bias toward the realistic question set, not everything the device exposes:

```yaml
date: 2026-09-15
schema_version: 1
source_device: victron-multiplus-ii-gx
collected_at: 2026-09-15T23:59:00+01:00
script_version: victron-poll-v1
pv_yield_kwh: 12.4
battery_soc_start_pct: 68
battery_soc_min_pct: 61
battery_soc_end_pct: 94
consumption_kwh: 9.1
alarms: []
gaps: []
```
12.4 kWh generated today, battery held above 60% throughout, no alarms.

This covers "how much did we generate," "how healthy is the battery," and "did anything go wrong" — the questions someone will actually ask — without the file growing to hold values nobody queries.

**Versioning the schema, if it changes:** if a field like `battery_temp_c` gets added in month nine, a rollup spanning the full year needs to know months one through eight simply don't have it — not silently treat missing values as zero, which would make a yearly average quietly wrong. A one-line note in the doc (or a `schema_version` field in the frontmatter itself) is enough to prevent this.

## 4. Worked Example — Local Weather Station

- **Data:** temperature, rainfall, wind, humidity.
- **Source:** a station with a genuine local gateway API (e.g. Ecowitt's GW2000-class gateway) rather than a cloud-only unit — polled directly over the local network/Tailscale, no external API call needed at all. This is the cleaner case than Victron's: no cloud dependency exists to weigh against.
- **Snapshot:** `/weather/2026-09-15.md`, daily or a few times a day.
- **Question it answers:** "did it rain this week," "what's the forecast pattern been."

## 4a. Worked Example — Web Archiving (External Reference Material)

**Note on scope:** unlike Victron and the weather station, this isn't sensor data — it's what surfaced separately as "human-scale data," which puts its actual *use* in Phase 1 territory (see `spec-home-ai.md` §2), even though the general pattern is documented here alongside the other Shape B examples. The template is track-agnostic by design (§Data Quality Principles); this is that principle in practice.

- **What it replaces:** the Mobile Webviewer plugin's role — capturing external articles/reference material without link rot — but as a decoupled tool rather than an in-app feature, so it doesn't tie the choice of markdown editor to whether that editor happens to have a built-in clipper.
- **Mechanism:** a self-hosted web archiving tool (e.g. ArchiveBox or Wallabag, both open source) runs on the node PC. A URL is sent to it from any device via the OS's normal share sheet — no dependency on which note app is installed. The tool fetches the page and extracts clean, readable text.
- **Snapshot:** a script converts newly-archived pages into dated markdown files (e.g. `/reference/2026-09-15-article-title.md`), same file conventions as everywhere else in the Shared/Node Library.
- **A genuine asymmetry worth knowing, versus the sensor examples:** because this captures *text*, it's immediately and fully processable by Open-WebUI's knowledge base the moment it's pushed — no captioning workaround needed the way images require (`archiver-protocol.md` §5). Text is the one format this pipeline handles natively.
- **Question it answers:** "what did that article say," "find the piece I saved about X" — retrieval over content that would otherwise have rotted behind a dead link.

## 5. Applying the Template to a New Data Source

Checklist for any future integration:
- [ ] **Schema first:** what fields actually matter, biased toward realistic questions rather than everything the source can report? (§3a)
- [ ] Does the source have a local network API, or only cloud? Prefer local (§2).
- [ ] What polling interval actually matters? (Solar/weather: daily is usually enough. Something safety-relevant might need hourly — still not live.)
- [ ] Define the snapshot file format and folder — keep it consistent with existing Shared Library conventions, structured data in frontmatter.
- [ ] Confirm `oikb` picks it up on its normal cycle — no special-casing per source.
- [ ] Where does the polling script itself live and run — same node PC as everything else (consistent with the household/Community AI single-PC pattern), a small always-on job alongside the others.
- [ ] If the schema ever changes, note the version/date of the change (§3a) — don't let it happen silently.

## 6. Rollups — Answering "Over Time" Questions

**The gap this closes:** the AI answers Shared Library questions through retrieval — finding the relevant document(s), not computing across them. That's naturally good at "what happened on one day" (find the one file) and naturally bad at "what's the trend over the last year" (would require synthesizing across hundreds of files at the moment the question is asked, a known weak point of retrieval-based systems generally — see the RAG literature's caution against asking retrieval to also do computation).

**The fix, following the same principle as everything else in this template:** a second deterministic script, run monthly and yearly, reads the raw daily snapshots and writes its own summary file — total yield, daily average, best/worst day, trend vs. the prior period. This is itself just another scheduled job feeding `oikb`, no different in kind from step 1 of §2. The AI answers long-period questions by reading an already-computed rollup, never by being asked to do statistics live.

```yaml
period: 2026-09
pv_yield_total_kwh: 342.6
pv_yield_daily_avg_kwh: 11.4
best_day: 2026-09-21
best_day_kwh: 18.9
worst_day: 2026-09-03
worst_day_kwh: 2.1
vs_prior_month_pct: +8
```

**Applies to any Shape B source, not just Victron** — a weather station's rollup would summarize rainfall totals and temperature extremes the same way. One rollup script per source, same monthly/yearly cadence, same "read the raw snapshots, write a summary, let the AI read that" shape.

---

*v4 — supersedes v3. Adds §4a: web archiving (ArchiveBox/Wallabag) as a worked example — the decoupled replacement for the Mobile Webviewer plugin's clipping role, applied in Phase 1 (see spec-home-ai.md §2) even though documented here as a Shape B pattern instance. First example that captures text rather than sensor readings — immediately knowledge-base-processable, no captioning workaround needed.*
