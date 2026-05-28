# AI Tools — Spec & Priority Order

Every tool the production line needs, in build order, with the MVP scope for each.

**Status:** Draft for Zaki meeting. Each tool has its own spec file (or stub for newer ones).

---

## Why this order

The line is only as fast as its bottleneck. The tools below are sequenced so that **each one unlocks the stage it serves before that stage becomes the bottleneck**. The matching tool comes first because without it, the whole production line can't run end-to-end.

---

## Execution timeline

**Launch target: 2–3 weeks.** Phase 1 tools are the must-haves to run the production line end-to-end. Everything after Phase 1 is an enhancement layered on top of a working line. We don't need all 16 tools to go live — we need 4.

---

## Priority list (16 tools)

### Phase 1 · Launch the line — 2 to 3 weeks

| # | Tool | Stage | Spec | Why first |
|---|---|---|---|---|
| 01 | **JD ↔ Database Matching** | Stage 3 (TAs) | [`01-jd-matching.md`](./01-jd-matching.md) | The whole line collapses without this. Returns top 20 candidates from Ready safety stock. |
| 02 | **Presentation Page Builder** | Stage 3 (TAs) | [`02-presentation-builder.md`](./02-presentation-builder.md) | Member-branded, 2nd-batch detection, feedback widget per candidate, publishes into Member Portal. |
| 03 | **Interview Report Creator + Auto-Enrichment + Candidate Email** | Stage 3 → DB | [`03-interview-report-creator.md`](./03-interview-report-creator.md) | Closes the learning loop. Every interview makes the DB smarter. Sends feedback email to candidate. |
| 13 | **Briefing Generator (NEW)** | Stage 3 (intake) | [`13-briefing-generator.md`](./13-briefing-generator.md) | Merges JD + kickoff transcript into a structured internal briefing that feeds the Matching tool. Precursor to Tool 01. |

### Phase 2 · Keep the safety stock alive — after launch

| # | Tool | Stage | Spec | Why next |
|---|---|---|---|---|
| 04 | **Availability Confirmation Bot** | Stage 2 (Candidate Ops) | [`04-availability-bot.md`](./04-availability-bot.md) | Weekly auto-check. Without this, the DB rots. Powers Green/Yellow/Red decay. |
| 05 | **Salary Refresh Bot** | Stage 2 | [`05-salary-refresh.md`](./05-salary-refresh.md) | Same pattern as availability. |
| 06 | **Resume Polish Workflow** | Stage 2 | [`06-resume-polish.md`](./06-resume-polish.md) | Massive throughput multiplier for Candidate Ops. |
| 12 | **Video Quality Verifier (NEW)** | Stage 2 | [`12-video-verifier.md`](./12-video-verifier.md) | Checks intro video quality, auto-emails candidates with recording suggestions if below standard. |

### Phase 3 · Sharpen the loop — ongoing

| # | Tool | Stage | Spec | Why later |
|---|---|---|---|---|
| 07 | **Intro Video Script Generator** | Stage 2 | [`07-video-script.md`](./07-video-script.md) | Improves video quality. Pairs with Tool 12. |
| 08 | **Warm-Keep Cadence** | Stage 2 | [`08-warm-keep.md`](./08-warm-keep.md) | Re-engagement automation. |
| 09 | **Interview Prep Generator** | Stage 3 | [`09-interview-prep.md`](./09-interview-prep.md) | Coaches candidates before member interviews. |
| 10 | **Demand Signal Analyzer** | Stage 4 → Sourcing | [`10-demand-signal-analyzer.md`](./10-demand-signal-analyzer.md) | Reads Granola transcripts + Member roadmaps (hiring + AI agents), writes structured forward-looking hires to Demand Signals table → flags Sourcing. |
| 14 | **AP Meeting Briefing + Roadmap Updater (NEW)** | Stage 4 | [`14-ap-briefing.md`](./14-ap-briefing.md) | Generates a briefing doc after every AP↔member meeting. Updates hiring roadmap + AI agents roadmap. Posts to Member Portal. |
| 16 | **Member Intelligence Enricher (NEW)** | Stage 4 (pre-onboarding) | [`16-member-intelligence-enricher.md`](./16-member-intelligence-enricher.md) | Pulls company info, sales transcripts, and LinkedIn signals to build a deep Member Brief the AP uses for the onboarding call. First impression matters. |

### Phase 4 · System intelligence — once data is flowing

| # | Tool | Stage | Spec | Why last |
|---|---|---|---|---|
| 11 | **Bottleneck Detector** | Cross-stage | [`11-bottleneck-detector.md`](./11-bottleneck-detector.md) | The operations dashboard. Only meaningful once the line is producing data. |
| 15 | **Supervisor Dashboard (Supply-Chain View) (NEW)** | Cross-stage | [`15-supervisor-dashboard.md`](./15-supervisor-dashboard.md) | Jesus's command center. All metrics, natural-language query field for ad-hoc queries, Kanban CRM-style view for HRs to enable sales-style forecasting. |

---

## Common architecture decisions

These apply to every tool below. Locking them once avoids re-litigating per tool.

### Model

- **Default:** Claude (Sonnet 4.7 for most, Opus for matching + interview enrichment where reasoning depth matters).
- **Why Claude:** Already integrated via MCP, already in Jesus's daily workflow, output quality is consistent for structured tasks.
- **Open to:** running specific tools on cheaper models (e.g. Haiku) for high-volume routine tasks like availability confirmation.

### Data layer

- **Single source of truth:** Database (see `../database/schema.md`).
- **All tools read/write via Database MCP** in v1. Direct API later if MCP throughput becomes a limit.
- **No tool maintains its own data store.** Anything they learn writes back into the Database schema.
- **Context sources for Stage 3/4 tools:** NotebookLM (member operations + voice), Granola (transcripts), Database (structured records).

### Trigger pattern

Three patterns, each tool uses one:

- **Pull (manual):** TA/AP clicks a button, tool runs once. Use for: matching, presentation builder, interview report, briefing generator.
- **Push (event):** Tool fires automatically when a record changes status. Use for: auto-enrichment, decay automations.
- **Cron (scheduled):** Tool runs on a fixed schedule. Use for: availability bot, salary refresh, warm-keep, decay cascade.

### Output handling

- **Every tool produces structured output** (JSON or Markdown) that maps to specific Database fields.
- **Audit trail:** every tool writes a `created_by_tool` and `created_at` field on records it modifies.

### Member Portal as front-end

The revamped Member Portal (UX/UI refresh of the existing portal) is the **single member-facing surface**. Presentation pages, briefing docs, hiring + AI agents roadmaps all live there. Members log in to one place, not separate URLs per HR.

### Error handling

- Tools fail safely. If matching can't find good candidates, it returns "no good matches, fall back to manual" rather than guessing.
- All tool errors log to a single `tool_run_log` table (TBD: add to schema if Zaki wants observability).

---

## What I need from Zaki

1. **Sign-off or pushback on the priority order.**
2. **Confirmation that Claude (via MCP) is the right primary model** for this stack, or a recommendation otherwise.
3. **Build estimate per tool** in his terms (days, not "complexity").
4. **Who actually codes these.** Zaki team build? Or Jesus + Claude Code? Probably a mix.
5. **Where do these live?** Standalone scripts? Inside the Member Portal app? Database extensions? Decide per tool.
6. **Member Portal revamp:** is that part of Zaki's scope or a separate workstream?

---

## Tool specs

See the individual spec files in this folder. Each one is a 1-page contract: purpose, input, output, MVP scope, integration points, what it deliberately doesn't do.
