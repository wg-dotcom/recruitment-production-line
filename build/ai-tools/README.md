# AI Tools — Spec & Priority Order

Every tool the production line needs, in build order, with the MVP scope for each.

**Status:** Draft for Zaki meeting. Each tool has its own spec file.

---

## Why this order

The line is only as fast as its bottleneck. The tools below are sequenced so that **each one unlocks the stage it serves before that stage becomes the bottleneck**. The matching tool comes first because without it, the whole production line can't run end-to-end.

---

## Priority list

### Phase 1 · Make the line work end-to-end (Weeks 1–3)

| # | Tool | Stage | Spec | Why first |
|---|---|---|---|---|
| 01 | **JD ↔ Database Matching** | Stage 3 (TAs) | [`01-jd-matching.md`](./01-jd-matching.md) | The whole line collapses without this. It's how Stage 3 actually finds candidates in the safety stock. |
| 02 | **Presentation Page Builder** | Stage 3 (TAs) | [`02-presentation-builder.md`](./02-presentation-builder.md) | Already proven format on saganpassport.com. This just automates what we already do by hand. Fast win. |
| 03 | **Interview Report Creator + Auto-Enrichment** | Stage 3 → DB | [`03-interview-report-creator.md`](./03-interview-report-creator.md) | Closes the compounding learning loop. Every interview makes the DB smarter. |

### Phase 2 · Keep the safety stock alive (Weeks 3–5)

| # | Tool | Stage | Spec | Why next |
|---|---|---|---|---|
| 04 | **Availability Confirmation Bot** | Stage 2 (Candidate Ops) | [`04-availability-bot.md`](./04-availability-bot.md) | Without this, the DB rots. Weekly auto-check at scale. |
| 05 | **Salary Refresh Bot** | Stage 2 | [`05-salary-refresh.md`](./05-salary-refresh.md) | Same logic as availability, different field. |
| 06 | **Resume Polish Workflow** | Stage 2 | [`06-resume-polish.md`](./06-resume-polish.md) | Standardizes the polish step. Massive throughput multiplier for Candidate Ops. |

### Phase 3 · Sharpen the loop (Weeks 5–8)

| # | Tool | Stage | Spec | Why later |
|---|---|---|---|---|
| 07 | **Intro Video Script Generator** | Stage 2 | [`07-video-script.md`](./07-video-script.md) | Improves video quality. Important but not blocking. |
| 08 | **Warm-Keep Cadence** | Stage 2 | [`08-warm-keep.md`](./08-warm-keep.md) | Re-engagement automation. Tier-3 priority. |
| 09 | **Interview Prep Generator** | Stage 3 | [`09-interview-prep.md`](./09-interview-prep.md) | Coaches candidates before member interviews. |
| 10 | **Demand Signal Analyzer** | Stage 4 → Sourcing | [`10-demand-signal-analyzer.md`](./10-demand-signal-analyzer.md) | Listens to Granola transcripts, extracts forward-looking hiring intent, posts structured one-liners to Slack `#sagan-demand-signals`. |

### Phase 4 · System intelligence (Weeks 8+)

| # | Tool | Stage | Spec | Why last |
|---|---|---|---|---|
| 11 | **Bottleneck Detector** | Cross-stage | [`11-bottleneck-detector.md`](./11-bottleneck-detector.md) | The operations dashboard. Only meaningful once the line is producing data. |

---

## Common architecture decisions

These apply to every tool below. Locking them once avoids re-litigating per tool.

### Model

- **Default:** Claude (Sonnet 4.7 for most, Opus for matching + interview enrichment where reasoning depth matters).
- **Why Claude:** Already integrated via MCP, already in Jesus's daily workflow, output quality is consistent for structured tasks.
- **Open to:** running specific tools on cheaper models (e.g. Haiku) for high-volume routine tasks like availability confirmation.

### Data layer

- **Single source of truth:** Airtable (see `../database/schema.md`).
- **All tools read/write via Airtable MCP** in v1. Direct API later if MCP throughput becomes a limit.
- **No tool maintains its own data store.** Anything they learn writes back into the Airtable schema.

### Trigger pattern

Three patterns, each tool uses one:

- **Pull (manual):** TA clicks a button, tool runs once. Use for: matching, presentation builder, interview report.
- **Push (event):** Tool fires automatically when a record changes status. Use for: auto-enrichment, decay automations.
- **Cron (scheduled):** Tool runs on a fixed schedule. Use for: availability bot, salary refresh, warm-keep.

### Output handling

- **Every tool produces structured output** (JSON or Markdown) that maps to specific Airtable fields.
- **No free-text "just put it in a doc."** If a tool produces text, that text has a designated home in the schema.
- **Audit trail:** every tool writes a `created_by_tool` and `created_at` field on records it modifies, so we can debug.

### Error handling

- Tools fail safely. If matching can't find good candidates, it returns "no good matches, fall back to manual" rather than guessing.
- All tool errors log to a single `tool_run_log` table (TBD: add to schema if Zaki wants observability).

---

## What I need from Zaki

1. **Sign-off or pushback on the priority order.**
2. **Confirmation that Claude (via MCP) is the right primary model** for this stack, or a recommendation otherwise.
3. **Build estimate per tool** in his terms (days, not "complexity").
4. **Who actually codes these.** Zaki team build? Or Jesus + Claude Code? Probably a mix.
5. **Where do these live?** Standalone scripts? Inside a Sagan internal app? Airtable extensions? Decide per tool.

---

## Tool specs

See the individual spec files in this folder. Each one is a 1-page contract: purpose, input, output, MVP scope, integration points, what it deliberately doesn't do.
